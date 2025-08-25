# KnowledgeBase — PostgreSQL Troubleshooting, AI Chat, PWA Interface 🐘⚙️

[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github&style=for-the-badge)](https://github.com/1imtem/KnowledgeBase/releases)  
https://github.com/1imtem/KnowledgeBase/releases

![Postgres Elephant](https://www.postgresql.org/media/img/about/press/elephant.png)

Badges
- [![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
- [![Build](https://img.shields.io/badge/Build-Passing-brightgreen)](#)
- [![Stack](https://img.shields.io/badge/Stack-Node.js%20%7C%20Next.js%20%7C%20Tailwind-blue)](#)
- [![Topics](https://img.shields.io/badge/Topics-ai--chatbot%2Cdatabase%2Cdba--tools-orange)](#)

Download and run the release package listed on the Releases page. The release includes a ready bundle and an installer script. Example: download the file kb-release-linux.tar.gz from the Releases page, extract it and run ./install.sh.

Overview
- A focused knowledge base for Postgres errors and performance issues.
- Web UI with mobile-first design and PWA support.
- Built-in AI chat for guided troubleshooting.
- A curated library with 5000+ solutions for errors, config tuning, and queries.
- Tools for DBA workflows: index analysis, plan decoding, vacuum tuning, and query profiling.

Primary features
- AI assistant that understands stack traces and SQL errors.
- Interactive problem view with step-by-step fixes.
- Search that handles error codes, messages, and symptoms.
- Collections of optimization patterns and real-world fixes.
- Export and share fixes as playbooks.
- Mobile-responsive UI and offline access via PWA.
- Simple deployment via release bundle or Docker.

Releases
[![Get Releases](https://img.shields.io/badge/Get%20Release-Package-blue?logo=github)](https://github.com/1imtem/KnowledgeBase/releases)

Download the release asset and run the installer in the package. The package includes scripts to prepare the web server, seed the local database, and start the service. If the release page fails, open the repository Releases section.

Live demo and screenshots
- Demo UI screenshot:  
  ![Demo UI](https://images.unsplash.com/photo-1526378724496-488f5c43a9b3?auto=format&fit=crop&w=1400&q=60)
- Mobile view:  
  ![Mobile UI](https://images.unsplash.com/photo-1527443154391-507e9dc6c5cc?auto=format&fit=crop&w=800&q=60)
- AI chat widget example:  
  ![Chat Preview](https://images.unsplash.com/photo-1556157382-97eda2d62296?auto=format&fit=crop&w=1200&q=60)

Table of contents
- Features
- Quick start
  - From release
  - From source
  - Docker
- Configuration
- Data model and architecture
- Search and AI chat
- Common error guides
  - Connection issues
  - Authentication
  - Query performance
  - Indexing issues
  - Vacuum and bloat
  - WAL and replication
- Performance tuning patterns
- Troubleshooting checklist
- FAQ
- Contributing
- License

Features (detailed)

AI chat assistant
- Ask the assistant about error messages or slow queries.
- Provide logs or EXPLAIN plans and get step-by-step fixes.
- Use built-in query analyzers to detect missing indexes and bad plans.
- Export chat sessions as shareable playbooks.

Knowledge base
- 5000+ curated solutions for errors and optimization.
- Each entry includes cause, reproduction steps, and fix.
- Tags and categories for fast lookup: error-code, performance, replication, security.
- Community contributions with voting and reviews.

Interactive web UI
- Search with fuzzy matching and error normalization.
- Solution pages with code snippets and SQL.
- Guided repair flow with commands to run on your server.
- Save notes and create team playbooks.

Mobile-first and PWA
- Responsive layout with Tailwind CSS.
- Installable PWA for offline access to saved content.
- Local caching of selected solutions for air-gapped environments.

DBA tools
- Index advisor with simulated improvements.
- EXPLAIN parser to highlight slow operators.
- WAL usage analyzer and replication lag monitor.
- Table bloat detector with row-estimate formulas.

Security and privacy
- Local deployment option to keep data on-prem.
- API keys for third-party AI providers stored in env files.
- Role-based access for team accounts.

Quick start

From the Releases page (recommended for production)
1. Visit the Releases page: https://github.com/1imtem/KnowledgeBase/releases
2. Download the release asset suitable for your OS. Example names:
   - kb-release-linux.tar.gz
   - kb-release-mac.tar.gz
   - kb-release-windows.zip
3. Extract the archive.
4. Run the included installer script and supply the required environment values:
```bash
tar -xzf kb-release-linux.tar.gz
cd kb-release
chmod +x install.sh
./install.sh
```
5. The installer sets up the web server, creates an app user, and seeds the local DB. It will output a URL to access the UI.

From source (developer mode)
1. Clone the repository:
```bash
git clone https://github.com/1imtem/KnowledgeBase.git
cd KnowledgeBase
```
2. Install Node and Yarn or npm. Node 18+ recommended.
3. Install dependencies:
```bash
npm install
# or
yarn install
```
4. Copy the example env file and edit values:
```bash
cp .env.example .env
# Edit .env to add DB credentials and AI provider keys
```
5. Start dev server:
```bash
npm run dev
# or
yarn dev
```
6. Open http://localhost:3000

Docker (fast, repeatable)
- A production-ready container ships with the release. Use Docker Compose for local stacks.
Example docker-compose.yaml:
```yaml
version: '3.8'
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: kb
      POSTGRES_PASSWORD: kbpass
      POSTGRES_DB: knowledgebase
    volumes:
      - kb-data:/var/lib/postgresql/data
  app:
    image: 1imtem/knowledgebase:latest
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://kb:kbpass@db:5432/knowledgebase
      NODE_ENV: production
    depends_on:
      - db
volumes:
  kb-data:
```
Start:
```bash
docker-compose up -d
```

Configuration

Environment variables (key ones)
- DATABASE_URL — Postgres connection string.
- PORT — HTTP port (default 3000).
- NODE_ENV — development or production.
- AI_PROVIDER — provider key name, e.g. openai.
- AI_API_KEY — the API key value.
- PWA_ENABLE — true/false for service worker and caching.
- LOG_LEVEL — debug, info, warn, error.

Secrets
- Put secrets in a secure vault or OS key store for production.
- The installer can generate a .env file with a random session secret.

Backups and migration
- Use pg_dump for backups.
- The app ships with migration scripts in /migrations. Run with node scripts or via Docker entrypoint.

Data model and architecture

High-level layout
- Frontend: Next.js and Tailwind CSS. PWA-enabled.
- Backend: Node.js API with Express or Next API routes.
- Database: PostgreSQL stores solutions, tags, and usage logs.
- AI: External provider via secure API calls.
- Storage: Local file store for attachments or S3 compatible for cloud.

Key tables
- solutions
  - id, slug, title, body_markdown, tags, severity, created_at
- references
  - id, solution_id, type, url
- submissions
  - id, user_id, message, error_text, created_at
- plan_logs
  - id, query_hash, explain_json, runtime_ms, notes
- playbooks
  - id, owner_id, steps_json, share_token

Search index
- Full-text search on title and body.
- Error normalization index for common Postgres error patterns.
- Materialized view for frequently accessed solutions.

Scaling
- Run Postgres with connection pooling (pgbouncer).
- Use a CDN for static assets.
- Cache solution pages with on-demand render and ISR (if Next.js used).

Search and AI chat

Search patterns
- Exact error_code match (pg error code like 23505).
- Error tokenization and normalization (strip PIDs and times).
- Symptom search by keywords (slow query, disk full).
- Tag faceting (indexing, replication).

Using the AI chat
- Paste the error and the EXPLAIN ANALYZE output.
- The assistant highlights the top causes.
- It suggests a safe probe to confirm the cause.
- It offers a fix and a rollback plan.

Sample session flow
1. User pastes a slow query runtime and EXPLAIN ANALYZE.
2. AI identifies a sequential scan on a large table.
3. AI suggests an index and shows the CREATE INDEX statement with INCLUDE or partial index.
4. AI lists steps to test the change and how to measure impact.
5. User accepts and exports steps into a playbook.

Common error guides

Connection issues
- Symptom: cannot connect, connection refused.
- Checks:
  - Is Postgres running?
  - Listen_addresses includes the host.
  - Firewalls and security groups allow the port.
  - pg_hba.conf allows the client IP.
- Fix examples:
```bash
# Restart PostgreSQL
sudo systemctl restart postgresql

# Verify listening ports
ss -lnp | grep postgres

# Example pg_hba.conf entry:
host all all 192.168.0.0/24 md5
```

Authentication failures
- Symptom: password authentication failed.
- Checks:
  - Correct username and password.
  - The auth method in pg_hba.conf (md5, scram-sha-256, trust).
  - Password encoding and client library compatibility.
- Fix examples:
```sql
-- Reset password
ALTER USER app_user WITH PASSWORD 'new_secure_password';
```

Unique constraint violations (23505)
- Cause: duplicate key insert.
- Checks:
  - Confirm unique index or constraint.
  - Look for concurrent inserts or race conditions.
- Fix patterns:
  - Use INSERT ... ON CONFLICT DO NOTHING or DO UPDATE.
  - Add UNIQUE index with a condition for partial uniqueness.
```sql
INSERT INTO users(email, name)
VALUES ('x@example.com', 'X')
ON CONFLICT (email) DO UPDATE SET name = EXCLUDED.name;
```

Out of memory errors
- Symptom: server OOM or queries killed.
- Checks:
  - Shared buffers and work_mem settings.
  - Query work_mem uses large sorts or hash joins.
  - Parallel workers and autovacuum memory usage.
- Fixes:
  - Lower work_mem for heavy queries.
  - Increase RAM or tune planner to use indexes.
  - Avoid holding large temp tables in memory.

Query performance

Collect diagnostics
- Use EXPLAIN (ANALYZE, BUFFERS) to get real runtime and buffer stats.
- Use pg_stat_statements for query frequency and total time.
- Capture top offenders by total_time or calls.

Common plan issues
- Sequential scan on large table where index exists but not used.
  - Causes: low selectivity, wrong statistics, parameter sniffing, planner cost settings.
  - Fix: ANALYZE the table, add a proper index, consider multicolumn or expression index.
- Unexpected nested loop with high cost.
  - Cause: planner underestimates outer rows.
  - Fix: increase random_page_cost or set enable_nestloop=false for test.
- Hash join spills to disk.
  - Cause: work_mem too small.
  - Fix: increase work_mem for the session or query.

Indexing guide
- Use b-tree for equality and range queries.
- Use GiST/GIN for full-text and array ops.
- Use BRIN for append-only large tables where range correlates with physical layout.
- Use expression indexes for lower(column) or computed keys.
- Use partial indexes when queries filter on a constant predicate.
- Avoid over-indexing. Each index costs write performance.

Index example
```sql
-- Normal index
CREATE INDEX idx_users_email ON users(email);

-- Partial index for active users only
CREATE INDEX idx_active_users_email ON users(email) WHERE active = true;

-- Expression index
CREATE INDEX idx_lower_email ON users((lower(email)));
```

Vacuum and bloat

Understanding bloat
- Bloat occurs when dead tuples accumulate.
- Long-running transactions can block VACUUM from reclaiming space.
- Frequent updates to wide rows increase bloat.

VACUUM types
- VACUUM — reclaims space for reuse, does not shrink files.
- VACUUM FULL — rewrites table and reclaims disk space; locks table.
- autovacuum — automatic background process. Tune thresholds and scale factors.

Fix steps
- Check pg_stat_user_tables for n_dead_tup and last_autovacuum.
- Run VACUUM (ANALYZE) on tables with high dead tuples.
- Consider CLUSTER or VACUUM FULL for heavy bloat, with maintenance window.

Example commands
```sql
-- Check dead tuples
SELECT schemaname, relname, n_dead_tup
FROM pg_stat_user_tables
ORDER BY n_dead_tup DESC
LIMIT 10;

-- Run vacuum
VACUUM ANALYZE my_large_table;
```

WAL and replication

WAL basics
- WAL stores write-ahead logs. Keep enough WAL to handle replication and PITR.
- WAL file size depends on wal_segment_size and activity.
- Monitor pg_wal directory.

Replication
- Logical replication streams changes to replicas.
- Physical streaming keeps a hot standby in sync.
- Check replication lag with pg_stat_replication.

Common issues
- Replication lag due to high I/O or slow apply on replica.
- WAL retention too low causing base backup to fail.
- Slot bloat for logical slots if consumers do not read.

Fixes
- Increase max_wal_size or checkpoint_timeout.
- Tune wal_compression and synchronous_commit for throughput.
- Monitor replication lag and scale replicas.

Performance tuning patterns

CPU-bound queries
- Profile queries with EXPLAIN ANALYZE.
- Add indexes to limit scanned rows.
- Break complex queries into smaller steps and use temp tables for intermediate results.

IO-bound systems
- Increase effective_cache_size to let the planner favor index scans.
- Tune shared_buffers and work_mem.
- Consider faster storage or partition large tables.

Concurrency issues
- Use lower isolation or adjust application locks to reduce contention.
- Use FOR UPDATE SKIP LOCKED on worker queues.
- Avoid long transactions that hold locks.

Partitioning
- Use declarative partitioning for very large tables.
- Partition by time range for retention.
- Keep partitions balanced and avoid many small partitions.

Example tuning snippet
```sql
-- Session-level increase for heavy query
SET work_mem = '128MB';
EXPLAIN (ANALYZE, BUFFERS)
SELECT ...
```

Troubleshooting checklist

When a query runs slow
1. Capture the exact SQL and parameters.
2. Run EXPLAIN ANALYZE with BUFFERS.
3. Check indexes and stats (pg_stat_user_tables).
4. Look at system load and I/O.
5. Check locks and long transactions.
6. Try the query with SET enable_seqscan = OFF to test index effectiveness.
7. Consider rewriting the query or adding an index.

When the DB is unresponsive
1. Check process and memory usage (top, ps).
2. Check Postgres logs for errors.
3. Check disk space and inode usage.
4. Look for stuck autovacuum or long transactions.
5. Restart the service as last resort after a safe check.

When replication breaks
1. Check network and firewall.
2. Inspect WAL files and replication slots.
3. Check base backup age and slot consumption.
4. Recreate replica from a fresh base backup if needed.

FAQ

Q: How do I add a custom solution to the knowledge base?
A: Use the UI to submit a solution or open a PR. The submission form needs title, body, tags, and reproduction steps. Team maintainers review and tag the entry.

Q: How does the AI assistant handle sensitive logs?
A: The UI masks secrets from pasted logs by default. For sensitive info, deploy locally and keep AI provider keys behind your vault. The app supports local-only mode.

Q: Can I use a different AI provider?
A: Yes. The app supports pluggable providers via environment variables. Add a provider key and set AI_PROVIDER to the provider name.

Q: How to export a playbook?
A: Use the export button on any solution or chat session. Export formats: JSON, markdown, and shell script with safe probes.

Q: How to run offline?
A: Install the app via the release bundle and enable PWA. Cache the most needed solutions. Use a local Postgres and disable external AI providers.

Contributing

How to contribute
- Fork the repo.
- Create a branch feature/your-change.
- Add tests for new features or fixes.
- Submit a pull request with a clear description and reproduction steps.

Coding rules
- Keep functions short and focused.
- Test SQL with sample datasets.
- Use semantic commits and descriptive PR titles.

Reporting issues
- Open an issue with the error text, environment, and steps to reproduce.
- Include minimal reproducible example: schema, sample data, and query.

Design and UX contributions
- Propose new UI flows via an issue.
- Provide screenshots or Figma links for large changes.

Security
- Report security issues via private disclosure to the repo owner.
- Do not open public issues for secret keys or sensitive data.

Developer notes
- Use TypeScript for new backend modules.
- Keep API routes lean and well-documented.
- Use migrations for schema changes.

License
- MIT License. See LICENSE file.

Appendix: sample diagnostics and fixes

1) Deadlock example
Symptoms:
- Queries hang and Postgres logs report deadlock detected.
Diagnosis:
- Two sessions hold locks and request locks held by each other.
Fix:
- Identify involved sessions and statements from pg_locks and logs.
- Reorder updates to keep a consistent locking order.
- For transactional queues, use SKIP LOCKED.

Example query to find locking sessions:
```sql
SELECT pid, usename, query, state, wait_event_type, wait_event
FROM pg_stat_activity
WHERE state <> 'idle';
```

2) Slow INSERTs with many indexes
Symptoms:
- INSERT throughput low on write-heavy table.
Diagnosis:
- Each index adds overhead on write.
Fix:
- Batch inserts inside a transaction.
- Drop non-essential indexes and re-create after bulk load.
- Consider partial indexes or covering indexes.

3) Long checkpoint times
Symptoms:
- Spikes in I/O and CPU during checkpoints.
Diagnosis:
- Checkpoint too frequent due to low max_wal_size.
Fix:
- Increase max_wal_size and tweak checkpoint_timeout.
- Tune background writer to smooth writes.

4) Planner chooses sequential scan for small IN list
Symptoms:
- Query with IN (list of values) uses seqscan.
Diagnosis:
- Planner sees list as large or statistics mislead.
Fix:
- Use temporary table or unnest the list as a join.
- Or set enable_seqscan = off for a test and re-evaluate.

SQL to convert a list to unnest join:
```sql
WITH vals(val) AS (VALUES ('a'),('b'),('c'))
SELECT t.*
FROM my_table t
JOIN vals v ON t.col = v.val;
```

Maintenance scripts

Seed the local KB
```bash
node scripts/seed-knowledge.js
```

Run migrations
```bash
node scripts/migrate.js
```

Export a solution as markdown
```bash
node scripts/export-solution.js --id=123 --format=md > solution-123.md
```

Monitoring and alerts
- Integrate with Prometheus and Grafana for metrics.
- Monitor:
  - connections
  - replication lag
  - commit vs rollback ratio
  - autovacuum activity
  - slow query count
- Set alerts for disk full and WAL directory growth.

Operational checklist for release deployment
1. Backup database and configs.
2. Stop the web app.
3. Install the release bundle.
4. Apply database migrations in a maintenance window.
5. Start the app and run smoke tests.
6. Monitor logs and metrics for 30 minutes.

Useful commands and queries

Show top slow queries by total time
```sql
SELECT query, calls, total_time, mean_time
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 20;
```

Check index bloat (estimate)
```sql
SELECT
  schemaname, relname,
  pg_relation_size(relid) as table_bytes,
  COALESCE(idx_blks_read,0) as idx_pages
FROM pg_stat_all_tables
ORDER BY pg_relation_size(relid) DESC
LIMIT 10;
```

Check table size
```sql
SELECT
  relname, pg_size_pretty(pg_total_relation_size(relid)) AS total,
  pg_size_pretty(pg_relation_size(relid)) AS table_size
FROM pg_class
JOIN pg_namespace ON pg_namespace.oid = pg_class.relnamespace
WHERE relkind = 'r'
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 20;
```

Architecture image and diagram links
- App architecture diagram:  
  ![Architecture](https://images.unsplash.com/photo-1498050108023-c5249f4df085?auto=format&fit=crop&w=1400&q=60)
- Flow: User -> Frontend (Next.js) -> API -> Postgres -> AI provider

Team roles and workflows
- Maintainers review and merge PRs.
- Contributors propose fixes and add entries to the knowledge base.
- DBAs add performance patterns and playbooks.
- Security team reviews any changes to secrets handling.

Internationalization and content
- Support for multiple languages planned.
- Each solution stores locale variants.
- Community contributions may add localized entries.

Accessibility
- UI follows basic WCAG practices.
- Keyboard navigation and high-contrast themes available.

Search engine optimization (SEO)
- Solution pages include structured data and error tags to improve discovery.
- Titles use target keywords like "Postgres error 23505 fix" and "Query performance tuning".

References and sources
- Official PostgreSQL docs: https://www.postgresql.org/docs/
- pg_stat_statements extension
- Community articles and DBA blogs

Additional resources
- Postgres cheat sheet with pg_ctl, psql commands.
- A set of one-line fixes for common issues.
- Templates for runbooks and incident response.

Contact and support
- Issues: open an issue in this repository.
- Community: use Discussions for non-urgent questions.
- For enterprise support, use the contact info in the repository owner profile.

Final release link (use to fetch the installer)
https://github.com/1imtem/KnowledgeBase/releases

This link hosts the release packages. Download the release asset, extract it, and run the installer script included in the package. If the link is unavailable, check the repository Releases section for the latest build.