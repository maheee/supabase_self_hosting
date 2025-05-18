# Supabase Notes

## Database

### create new schema for private data
```sql
CREATE SCHEMA data;

GRANT USAGE ON SCHEMA data TO anon, authenticated, service_role;

GRANT ALL ON ALL TABLES IN SCHEMA data TO anon, authenticated, service_role;
GRANT ALL ON ALL ROUTINES IN SCHEMA data TO anon, authenticated, service_role;
GRANT ALL ON ALL SEQUENCES IN SCHEMA data TO anon, authenticated, service_role;

ALTER DEFAULT PRIVILEGES FOR ROLE postgres, supabase_admin IN SCHEMA data GRANT ALL ON TABLES TO anon, authenticated, service_role;
ALTER DEFAULT PRIVILEGES FOR ROLE postgres, supabase_admin IN SCHEMA data GRANT ALL ON ROUTINES TO anon, authenticated, service_role;
ALTER DEFAULT PRIVILEGES FOR ROLE postgres, supabase_admin IN SCHEMA data GRANT ALL ON SEQUENCES TO anon, authenticated, service_role;
```

### remove permissions from public schema so it is readonly
```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO service_role;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON ROUTINES TO service_role;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO service_role;

ALTER DEFAULT PRIVILEGES IN SCHEMA public REVOKE ALL ON TABLES FROM anon, authenticated;
ALTER DEFAULT PRIVILEGES IN SCHEMA public REVOKE ALL ON ROUTINES FROM anon, authenticated;
ALTER DEFAULT PRIVILEGES IN SCHEMA public REVOKE ALL ON SEQUENCES FROM anon, authenticated;

ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO anon, authenticated;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT EXECUTE ON ROUTINES TO anon, authenticated;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON SEQUENCES TO anon, authenticated;

REVOKE ALL ON ALL TABLES IN SCHEMA public FROM anon, authenticated;
REVOKE ALL ON ALL ROUTINES IN SCHEMA public FROM anon, authenticated;
REVOKE ALL ON ALL SEQUENCES IN SCHEMA public FROM anon, authenticated;

GRANT SELECT ON ALL TABLES IN SCHEMA public TO anon, authenticated;
GRANT EXECUTE ON ALL ROUTINES IN SCHEMA public TO anon, authenticated;
GRANT SELECT ON ALL SEQUENCES IN SCHEMA public TO anon, authenticated;
```

### create an index
```sql
CREATE INDEX "books_reading_session_book_id" ON "data"."books_reading_sessions" USING BTREE ("book_id");
```

### create a new view
```sql
CREATE VIEW public.books_reading_sessions WITH (security_invoker = on) AS SELECT * FROM data.books_reading_sessions;
```

### set permissions for a view
```sql
ALTER VIEW public.books SET (security_invoker = on);
```

### see tables, policies and grants
```sql
SELECT * FROM pg_tables;
SELECT * FROM pg_policies;
SELECT * FROM information_schema.role_table_grants WHERE table_schema IN ('public', 'data') ORDER BY table_schema, table_name, grantee;
```
