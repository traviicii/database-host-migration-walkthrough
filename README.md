# PostgreSQL Host Migration Walkthrough 
---

A step-by-step guide to migrating a pre-existing database from one host to another.

## Choose a new database provider
Below are a few potential options. In this walkthrough, *we'll be using ElephantSQL -> Supabase as an example*.

- Amazon RDS for AWS
- Azure Database for PostgreSQL on Microsoft Azure
- Google Cloud SQL
- Heroku Postgres
- Supabase

> We'll be creating a backup of the database that we want to transfer, and using it to populate our new database instance.


## 1. Install PostgreSQL tools on your machine

### First see if you already have the required tools installed

#### Open a new terminal or CMD prompt and run:

```bash
pg_dump --version
psql --version
```

> If you have versions for these tools installed, you can move onto step 2 -> [Generating a backup file](#2-generate-a-backup-using-pg_dump)

### If necessary, install psql (PostgreSQL)
[PostgreSQL Documentation](https://www.postgresql.org/)


#### Mac install using Homebrew
> [Homebrew Documentation](https://brew.sh/)

This command installs PostgreSQL along with `pg_dump`, `psql`, and other PostgreSQL tools.
```bash
brew install postgresql
```

#### Windows

1. Go to the [PostgreSQL download page](https://www.postgresql.org/download/windows/).
2. You'll be directed to a page where you can download the installer provided by EnterpriseDB, which includes all the necessary tools like `pg_dump` and `psql`.
3. Execute the downloaded installer file.

Follow the on-screen instructions. The installer will ask you to select components to install. Make sure to include the PostgreSQL server, the command line tools, and any additional components you might need.

>During installation, you will be prompted to set a password for the PostgreSQL superuser (postgres), and you can also specify the port on which PostgreSQL will listen (default is 5432). We'll be using this default port in this walkthrough.
The installer usually sets up the path environment variable for PostgreSQL automatically, which means you can run `pg_dump` and `psql` from any command prompt window.

### Verify installation

```bash
pg_dump --version
psql --version
```

## 2. Generate a backup using `pg_dump`

### Construct your backup command
Gather your database details needed to run this command. Be sure to use your current database!

```bash
pg_dump -h [old-host] -U [username] -d [database-name] -f backup.sql -W --port=5432
```

- `pg_dump`: This is the command itself, which invokes the PostgreSQL database backup utility.

- `-h [old-host]`: This option specifies the hostname of the database server. In the context of your command, replace [old-host] with the actual hostname, IP address, or server where your ElephantSQL database is hosted. This could be a URL or an IP address.

- `-U [username]`: This option specifies the username to connect to the database. <mark>***This is different than the username associated with your overall account***</mark>. Check database details and replace [username] with the actual username that has access to the database you want to back up.

- `-d [database-name]`: This option specifies the name of the database to back up. <mark>***It's possible that this is the same as the username above***</mark>. Check your database details to confirm if the username and default database name differ. Replace [database-name] with the name for your database.

- `-f backup.sql`: This option directs pg_dump to write the output (the dump file) to a file rather than to standard output. backup.sql is the name of the file where the dump will be saved. You can specify a different file name or path if you prefer to save the file somewhere specific.

- `-W` option will prompt you for the database password when you run the command

### Execute the Command
1. Run the command above.
2. Enter the database password when prompted.

### If you've constructed your backup command correctly, you should see a `backup.sql` file in the location your terminal was pointed at during the execution of the backup command.

> ***Create a new PostgreSQL database instance with your chosen provider before proceding.***


## 3. Import the backup into the new database using `psql`

### Gather Your New Database Details

You'll need the following information from your Supabase project or the database you're transferring to:

- Host: This is the URL of your Supabase database instance.
- Username: Typically, this is postgres or another administrator username provided by Supabase.
- Database Name: The specific database into which you want to import your backup.
- Password: Your database password.

You can find these details in the Supabase dashboard under the "Project Settings" tab, then "Database".

### Construct your import command

>  <mark>***This command is slightly different than before***</mark>

Fill in the placeholders with your database details. 
```bash
psql -h [new-host] -U [new-username] -d [new-database-name] -f backup.sql
```

### Execute the Command
1. Run the command above.
2. Enter the database password when prompted.

## 4. Verify Data Integrity
Check that all data has been transferred correctly. Run test queries and compare the results with your old database to ensure integrity.

## 5. Update Application Configurations
Update any application environment or service configurations that interact with the database to point to the new database instance.

# Your database migration is complete! 🚀✨