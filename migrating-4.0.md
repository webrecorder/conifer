## Migrating from Conifer 3.x to Conifer 4.x

The Conifer 4.x release introduced significant changes to the data model.

A migration script is provided which will migrate a local Conifer 3.x installation to 4.x.

(The version of Conifer can be found in the [conifer package init](rhizome-conifer/conifer/__init__.py))

The migration can be performed as follows:

1) Stop old Conifer 3.x

2) Pull the latest Conifer 4.x from github (this branch)

3) Update `REDIS_BASE_URL` in `wr.env` to redis://redis/2 to use a blank new Redis DB

4) Add `STORAGE_ROOT=/data/storage/` to wr.env

5) Recreate Conifer containers via `./recreate.sh`, which will remove old containers, build new ones.

6) Run `docker exec -it app python ./migration_scripts/migrate4.0.py --new-redis=redis://redis/2`

### What Migration Script Does

The migration script will do the following:

- All user, collections, recordings are copied into the new Redis DB (DB 2 per settings above)
  To use a different Redis DB or a remote Redis, specify a different `redis://` url.

- All WARC and CDX data is copied to `./data/storage` (specified via `STORAGE_ROOT` env var) and organized by collections to match the new data model and directory layout.

- A new public list is created for all non-hidden Conifer 3.x bookmarks. Hidden bookmarks are still pages, but are not added to a list.

- Old Data (usually in DB 1) is not deleted and existing WARCs under `./data/warcs` are not deleted. These can be deleted manually after migration has been performed successfully.

### Other Options -- Download and Upload

WARCs downloaded from Conifer 3.x will still be compatible and can be uploaded into Conifer 4.x.

If migrating just a single or a few collections, it may make sense to create a fresh Conifer 4.x and upload WARCs collections individually.

### Advanced Options

The migration script also allows for migrating a single user, or a single collection.
The script also supports migrating via S3 copy if the source and target are using `DEFAULT_STORAGE=s3`

For more information on these migration options, see:

`docker exec -it app python ./migration_scripts/migrate4.0.py -h`

Please feel free to open an issue or contact us for additional questions about migration.
