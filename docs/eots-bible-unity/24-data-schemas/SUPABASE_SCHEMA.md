# Supabase Database Schema

## Tables

### players
```sql
CREATE TABLE players (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    steam_id TEXT UNIQUE,
    psn_id TEXT,
    xbox_id TEXT,
    display_name TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT now(),
    last_login TIMESTAMPTZ,
    total_echoes_earned BIGINT DEFAULT 0,
    total_runs INT DEFAULT 0,
    total_deaths INT DEFAULT 0,
    highest_heat_cleared INT DEFAULT 0,
    codex_entries_unlocked INT DEFAULT 0,
    spore_pass_active BOOLEAN DEFAULT false,
    spore_pass_expires TIMESTAMPTZ
);
```

### servers
```sql
CREATE TABLE servers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    region TEXT NOT NULL,  -- us-east, eu-west, etc.
    created_at TIMESTAMPTZ DEFAULT now(),
    player_count INT DEFAULT 0,
    true_ending_vote TEXT,  -- NULL, 'FEED', 'STARVE'
    true_ending_voted_at TIMESTAMPTZ
);
```

### skill_tree_nodes
```sql
CREATE TABLE skill_tree_nodes (
    id SERIAL PRIMARY KEY,
    server_id UUID REFERENCES servers(id),
    branch TEXT NOT NULL,  -- expansion, evolution, ecology, infrastructure, lore
    depth INT NOT NULL,    -- 1-20
    current_echoes BIGINT DEFAULT 0,
    required_echoes BIGINT NOT NULL,
    unlocked BOOLEAN DEFAULT false,
    unlocked_at TIMESTAMPTZ,
    UNIQUE(server_id, branch, depth)
);
```

### player_contributions
```sql
CREATE TABLE player_contributions (
    id BIGSERIAL PRIMARY KEY,
    player_id UUID REFERENCES players(id),
    server_id UUID REFERENCES servers(id),
    branch TEXT NOT NULL,
    echoes_contributed BIGINT NOT NULL,
    contributed_at TIMESTAMPTZ DEFAULT now()
);
-- Index for leaderboard queries
CREATE INDEX idx_contributions_server_player 
    ON player_contributions(server_id, player_id);
```

### codex_entries
```sql
CREATE TABLE codex_entries (
    player_id UUID REFERENCES players(id),
    entry_id TEXT NOT NULL,  -- 'symbiosis_001', 'corruption_023', etc.
    unlocked_at TIMESTAMPTZ DEFAULT now(),
    PRIMARY KEY (player_id, entry_id)
);
```

### sessions
```sql
CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    server_id UUID REFERENCES servers(id),
    zone_id INT NOT NULL,
    heat_level INT DEFAULT 0,
    started_at TIMESTAMPTZ DEFAULT now(),
    ended_at TIMESTAMPTZ,
    outcome TEXT,  -- 'clear', 'death', 'abandon'
    player_count INT NOT NULL
);
```

### session_players
```sql
CREATE TABLE session_players (
    session_id UUID REFERENCES sessions(id),
    player_id UUID REFERENCES players(id),
    class TEXT NOT NULL,
    crest TEXT NOT NULL,
    infection_start INT DEFAULT 0,
    infection_end INT,
    echoes_earned INT DEFAULT 0,
    deaths INT DEFAULT 0,
    PRIMARY KEY (session_id, player_id)
);
```

### leaderboards
```sql
CREATE TABLE leaderboards (
    id BIGSERIAL PRIMARY KEY,
    server_id UUID REFERENCES servers(id),
    player_id UUID REFERENCES players(id),
    category TEXT NOT NULL,  -- 'heat_clear', 'speedrun', 'contribution'
    score BIGINT NOT NULL,
    metadata JSONB,  -- { class, crest, heat, time_seconds }
    recorded_at TIMESTAMPTZ DEFAULT now()
);
CREATE INDEX idx_leaderboards_category_score 
    ON leaderboards(server_id, category, score DESC);
```

## Row Level Security (RLS)

```sql
-- Players can read own data, server members can read shared data
ALTER TABLE players ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Players can read own profile" ON players
    FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Players can update own profile" ON players
    FOR UPDATE USING (auth.uid() = id);

-- Skill tree is readable by all server members
ALTER TABLE skill_tree_nodes ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Server members can read tree" ON skill_tree_nodes
    FOR SELECT USING (true);  -- Public read
CREATE POLICY "Authenticated can contribute" ON skill_tree_nodes
    FOR UPDATE USING (auth.role() = 'authenticated');
```

## Redis Keys (Real-Time Cache)

```
# Session state (TTL: 1 hour)
session:{id}:state          → JSON { zone, tick, player_count }
session:{id}:player:{pid}   → JSON { position, infection, hp }

# Matchmaking queue (sorted set, score = queue_time)
matchqueue:{region}:{zone}  → ZSET { player_id: timestamp }

# Community Tree cache (flush to Supabase every 60s)
tree:{server_id}:{branch}:current  → INT (current echo count)
tree:{server_id}:{branch}:depth    → INT (current depth)

# Player presence (TTL: 5 min, refreshed on heartbeat)
presence:{server_id}:{player_id}   → JSON { zone, last_seen }

# Hub positions (cosmetic, 2Hz update)
hub:{server_id}:positions          → HASH { player_id: "x,y" }
```
