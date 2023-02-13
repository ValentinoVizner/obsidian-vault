# Snowflake


## Custom User Functions
### Creating an In-line Python UDF
For an in-line UDF, you supply the Python source code as part of the CREATE FUNCTION statement.

For example, the following statement creates an in-line Python UDF that adds one to a given integer:

```sql
create or replace function addone(i int)
returns int
language python
runtime_version = '3.8'
handler = 'addone_py'
as
$$
def addone_py(i):
  return i+1
$$;
```
Call the UDF:

```sql
select addone(10);
```
### Granting Privileges on the Function

For any role other than the owner of the function to call the function, the owner must grant the appropriate privileges to the role.

The [GRANT](https://docs.snowflake.com/en/sql-reference/sql/grant-privilege.html) statements for a Python UDF are essentially identical to the GRANT statements for other UDFs, such as JavaScript UDFs.

```sql
GRANT USAGE ON FUNCTION my_python_udf(number, number) TO my_role;
```