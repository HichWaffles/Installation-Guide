# Big Data Lab Setup and Data Import Guide (Windows)

This document is organized database-by-database so each section is self-contained.

Download source for installers and datasets:

[Google Drive folder (class download source)](https://drive.google.com/drive/folders/1Xw4FTvxY-SCzh7THhiyTEFIQ1WUajo-p?usp=sharing)

How to open PowerShell and Ubuntu WSL (used in this guide):

1. Press **Win + R**.
2. Type `powershell`.
3. Press **Enter**.

Note: verification steps are optional in this guide. They help you confirm your import, but they are not required to finish setup.

---

## 0. Start In The Correct Folder First

Before installing or importing anything, make sure you are inside the extracted class folder.

1. Decompress/unzip the downloaded class files first.
2. Open PowerShell.
3. Change directory to wherever you extracted the folder (this example is intentionally a placeholder and will fail until you replace it with your real path):

```powershell
cd "C:\REPLACE\THIS\WITH\YOUR\big_data_shared"
```

4. Run:

```powershell
dir
```

5. Open Ubuntu WSL as well (press **Win + R**, type `wsl`, press **Enter**) and move to the same folder using `/mnt/c/...` style path:

```bash
cd "/mnt/c/REPLACE/THIS/WITH/YOUR/big_data_shared"
ls -l
```

6. Use the correct reference screenshot for the terminal you are using:

   - PowerShell (`dir`) reference:
![Initial dir reference](<screenshots/initial_dir.png>)

   - Ubuntu WSL (`ls -l`) reference:
![WSL ls -l reference](<screenshots/WSL_ls.png>)

Only continue once your folder listing matches the corresponding screenshot.

---

## 1. MongoDB (Install -> Import -> Verify)

### 1.1 Install MongoDB + Compass

Installer files:

- `mongodb-windows-x86_64-8.0.8-signed.msi`
- `mongodb-compass-1.46.0-win32-x64.exe`

Steps:

1. Run `mongodb-windows-x86_64-8.0.8-signed.msi`.
2. Choose **Complete** setup.
3. Keep **Install MongoDB as a Service** enabled.
4. Run `mongodb-compass-1.46.0-win32-x64.exe` (only if you did not select **Install MongoDB Compass** in Step 4 of the MongoDB installer screenshots below).

Follow these screenshots in order while installing MongoDB:

1. Start the installer as shown below.
![Step 1 - open MongoDB installer](<screenshots/mongo/mongo_1.png>)

2. Choose setup type **Complete**.
![Step 2 - select Complete](<screenshots/mongo/mongo_2(select complete).png>)

3. Leave the default options unchanged.
![Step 3 - leave defaults](<screenshots/mongo/mongo_3(leave defaults).png>)

4. Keep **Install MongoDB Compass** checked, then click **Next** and **Install**.
![Step 4 - keep Compass checked](<screenshots/mongo/mongo_4(install mongodb compass must be checked).png>)

Verify installation:

```powershell
Get-Service MongoDB
```

### 1.2 Import Mongo Data

Dataset files:

- `data\mongo\movies.json`
- `data\mongo\comments.json`
- `data\mongo\sessions.json`
- `data\mongo\theaters.json`
- `data\mongo\users.json`

Use MongoDB Compass (no terminal commands needed):

1. Open MongoDB Compass.
2. Click **Add new connection**.
![Compass Step 1 - add new connection](<screenshots/mongo/compass_1(click add new connection).png>)
3. Leave the connection settings as default for `mongodb://localhost:27017`, then click **Save & Connect**.
![Compass Step 2 - defaults then Save & Connect](<screenshots/mongo/compass_2 (leave as defaults and connect).png>)
4. Create database `mflix` and set the first collection to `movies` in the same dialog.
![Compass Step 3 - click Create Database](<screenshots/mongo/compass_3 (click create database).png>)
5. Your first collection (`movies`) is created with the database. The full collection set should be:
![Compass Step 4 - set db and first collection](<screenshots/mongo/compass_4 (db name mflix collection name movies).png>)
   - `movies`
   - `comments`
   - `sessions`
   - `theaters`
   - `users`
6. For the second and later collections only, with `mflix` selected, click **Create Collection**.
![Compass Step 5 - create collection from selected database](<screenshots/mongo/compass_8 (with the database we created selected click on create collection).png>)
7. Name the collection accordingly (for example `comments`, then `sessions`, then `theaters`, then `users`).
![Compass Step 6 - name collection](<screenshots/mongo/compass_8 (name collection accordingly).png>)
8. Open a collection, then click **Add Data** -> **Import JSON or CSV file**.
![Compass Step 7 - click Add Data then Import](<screenshots/mongo/compass_5 (click add data then import csv or json).png>)
9. Select the matching file from `data\mongo`:
![Compass Step 8 - select appropriate data file](<screenshots/mongo/compass_6 (select appropriate data).png>)
   - `movies` -> `movies.json`
   - `comments` -> `comments.json`
   - `sessions` -> `sessions.json`
   - `theaters` -> `theaters.json`
   - `users` -> `users.json`
10. Keep default import settings unless your instructor says otherwise, then click **Import**.
![Compass Step 9 - click Import](<screenshots/mongo/compass_7 (click Import).png>)
11. Repeat from Step 6 for each remaining collection/file pair until all five are imported.

### 1.3 Optional: Verify Mongo Data (not required)

In MongoDB Compass, open `mflix` and confirm each collection (`movies`, `comments`, `sessions`, `theaters`, `users`) has documents.

![Mongo verify - all collections present](<screenshots/mongo/compass_9 (final screenshots shows all collections that should be present).png>)

---

## 2. Cassandra (Install -> Import -> Verify)

### 2.1 Install Cassandra 4.1.10

OpenJDK prerequisite (Ubuntu WSL):

1. Install OpenJDK before starting Cassandra:

```bash
sudo apt update
sudo apt install -y openjdk-17-jdk
```

2. Verify Java installation:

```bash
java -version
```

Add your verification screenshot here after running `java -version`:

![OpenJDK verify - java version](<screenshots/cassandra/openjdk_verify.png>)

Installer archive:

- `apache-cassandra-4.1.10-bin.tar.gz`

Note: Cassandra is distributed as a `.tar.gz` archive here (not a Windows `.exe` installer).

Steps:

Run the following Bash steps in Ubuntu WSL.

1. From the folder that contains `apache-cassandra-4.1.10-bin.tar.gz`, decompress with:

```bash
tar -xzvf apache-cassandra-4.1.10-bin.tar.gz
```

![Cassandra install - decompress](<screenshots/cassandra/install_1 (decompress).png>)

2. Go into the Cassandra `bin` folder:

```bash
cd apache-cassandra-4.1.10/bin
```

3. Start Cassandra and keep that terminal open:

```bash
./cassandra
```

![Cassandra install - run and keep open](<screenshots/cassandra/install_2 (cd and run leave open).png>)

### 2.1.1 Optional: Run cqlsh with a Python virtual environment

If you prefer to use `cqlsh` directly instead of DataGrip, run these commands in Ubuntu WSL.

1. Go to the Cassandra `bin` directory (same location as `cqlsh`).

```bash
cd apache-cassandra-4.1.10/bin
```

2. Create a virtual environment:

```bash
python3 -m venv cqlsh-env
```

3. Activate it:

```bash
source cqlsh-env/bin/activate
```

4. Install dependencies:

```bash
pip install six cassandra-driver
```

5. Run cqlsh without bundled Python packages:

```bash
CQLSH_NO_BUNDLED=true ./cqlsh
```

![cqlsh venv setup](<screenshots/cassandra/set up python environment for cqlsh.png>)
![cqlsh run command](<screenshots/cassandra/run cqlsh.png>)

### 2.2 Install DataGrip

Installer executable:

- `datagrip-2026.1.exe`

Use your three DataGrip installation screenshots in this order:

1. Start the DataGrip installer.
![DataGrip install step 1](<screenshots/cassandra/dg_1.png>)
2. Continue with default/recommended installer options.
![DataGrip install step 2](<screenshots/cassandra/dg_2.png>)
3. Finish installation and launch DataGrip.
![DataGrip install step 3](<screenshots/cassandra/dg_3.png>)

### 2.3 Import Cassandra Data

Dataset files:

- `data\cassandra\c.cql`
- `data\cassandra\watch_events_synthetic.csv`

Use DataGrip for Cassandra import/query workflow (not CQL shell).

Keep Cassandra running from Section 2.1, then complete import/query steps in DataGrip.

Follow these DataGrip steps:

1. On first launch, skip sample import/setup.
![DataGrip import step 1 - skip import](<screenshots/cassandra/id_1 (skip import).png>)
2. Create a new project.
![DataGrip import step 2 - new project](<screenshots/cassandra/id_2 (new project).png>)
3. Add a new Apache Cassandra data source.
![DataGrip import step 3 - new Cassandra data source](<screenshots/cassandra/id_3 (new apache cassandra data source).png>)
4. Set authentication to **No Auth**.
![DataGrip import step 4 - no auth](<screenshots/cassandra/id_4 (set authentication to no auth).png>)
5. Click **Test Connection**, then allow DataGrip to download required driver files.
![DataGrip import step 5 - test connection](<screenshots/cassandra/id_5 (click test connection and download driver files).png>)
6. Wait for the driver download to complete.
![DataGrip import step 6 - wait for download](<screenshots/cassandra/id_6 (wait for it to download).png>)
7. Confirm success, then click **OK**.
![DataGrip import step 7 - success and OK](<screenshots/cassandra/id_6 (you should see success message then click OK).png>)
8. Open SQL console and paste the full contents of `data/cassandra/c.cql`.
![DataGrip import step 8 - paste c.cql](<screenshots/cassandra/id_7 (copy contents of data(slash)cassandra(slash)c(dot)cql into console).png>)
9. Execute the CQL statements one by one to create keyspace/table.
![DataGrip import step 9 - execute CQL](<screenshots/cassandra/id_8 (execute commands one by one).png>)
10. Start table import from file(s).
![DataGrip import step 10 - import from file](<screenshots/cassandra/id_9 (import data from file(s)).png>)
11. Select `data/cassandra/watch_events_synthetic.csv` as the source file.
![DataGrip import step 11 - select CSV](<screenshots/cassandra/id_10 (select CSV file).png>)
12. Confirm import settings and click **OK**.
![DataGrip import step 12 - click OK](<screenshots/cassandra/id_10 (click ok).png>)
13. Refresh the table to verify imported rows are visible.
![DataGrip import step 13 - refresh table](<screenshots/cassandra/id_11 (you can now refresh to see the data in the table).png>)

---

## 3. Neo4j (Install + Load Dump -> Verify)

### 3.1 Install Neo4j Desktop And Load The Dump During Instance Creation

Installer file:

- `neo4j-desktop-2.1.1-x64.exe`

Follow these setup steps in order:

1. Run `neo4j-desktop-2.1.1-x64.exe`.
![Neo4j install](<screenshots/neo4j/run installation.png>)
2. Create a new local instance.
![Neo4j create instance](<screenshots/neo4j/n_1 (create instance).png>)
3. During instance creation, choose **Load from dump**.
![Neo4j load from dump](<screenshots/neo4j/n_2 (load from dump).png>)
4. Select `data\neo4j\recommendations-50.dump`.
![Neo4j select dump file](<screenshots/neo4j/n_3 (select dump file).png>)
5. Set a password for the instance.
![Neo4j choose password](<screenshots/neo4j/n_4 (choose password).png>)
6. Click **Create** and wait for startup to finish.
![Neo4j wait for create](<screenshots/neo4j/n_5 (wait after you already clicked create).png>)

### 3.2 Optional: Verify Neo4j Data (not required)

Optional verification steps:

1. Connect to the instance and open **Explore**.
![Neo4j connect and explore](<screenshots/neo4j/n_6 (connect, explore).png>)
2. Select any available view.
![Neo4j select any graph/view](<screenshots/neo4j/n_7 (select any).png>)
3. Run the action shown in the screenshot.
![Neo4j run query](<screenshots/neo4j/n_8 (run query).png>)
4. If nodes are visible, the dump load is successful.
![Neo4j nodes visible](<screenshots/neo4j/n_9 (if you see nodes you're good).png>)

---

