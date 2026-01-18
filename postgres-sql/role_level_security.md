# Role level security

- [Postgresql website](https://www.postgresql.org/docs/current/ddl-rowsecurity.html)

Row security policies can be specific to commands, or to roles, or to both. A policy can be specified to apply to ALL commands, or to SELECT, INSERT, UPDATE, or DELETE. Multiple roles can be assigned to a given policy, and normal role membership and inheritance rules apply.

To specify which rows are visible or modifiable according to a policy, an expression is required that returns a Boolean result. This expression will be evaluated for each row prior to any conditions or functions coming from the user's query. (The only exceptions to this rule are leakproof functions, which are guaranteed to not leak information; the optimizer may choose to apply such functions ahead of the row-security check.) Rows for which the expression does not return true will not be processed. Separate expressions may be specified to provide independent control over the rows which are visible and the rows which are allowed to be modified. Policy expressions are run as part of the query and with the privileges of the user running the query, although security-definer functions can be used to access data not available to the calling user.

Policies are created using the CREATE POLICY command, altered using the ALTER POLICY command, and dropped using the DROP POLICY command. To enable and disable row security for a given table, use the ALTER TABLE command.

When multiple policies apply to a given query, they are combined using either OR (for permissive policies, which are the default) or using AND (for restrictive policies). The OR behavior is similar to the rule that a given role has the privileges of all roles that they are a member of. Permissive vs. restrictive policies are discussed further below.

### Example.

- Users can only read and update their own profile.
- Moderators can only read and update their own profile and
  all user profiles.
- Admin can do anything.

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY; -- Enable rls.
SET ROLE moderator; -- Set the user role.
SET LOCAL app.user_id=1; -- in a transaction block.
```

Incase set role moderator fails you need to add the user to that role.

```sql
GRANT MODERATOR TO bilal;
```

```sql
-- Users can only update their own profile.
CREATE POLICY user_can_update_own_profile ON users FOR UPDATE USING (id = current_setting('app.user_id')::bigint AND role = 'app_user');
CREATE POLICY moderator_can_update_user_profiles_and_own_profile ON users FOR UPDATE USING ((role = 'app_user') OR id = current_setting('app.user_id')::bigint AND role = 'moderator');
CREATE POLICY admins_can_update_any_body_profile ON users FOR UPDATE USING (role IN ('app_user', 'moderator'));
```

### View policies

```sql
SELECT *
FROM pg_policies
WHERE schemaname = 'public'
  AND tablename = 'users';

--- psql shortcust
\dp
\dp public.users

--- all pg policies
SELECT *
FROM pg_policies;
```
