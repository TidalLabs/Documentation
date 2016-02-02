1. From mongo1, run the cron-stagdump script
2. From mongo2, run the stagdump script
3. Transfer mongo stagdump to dev server /opt/resources
4. From crawler, run pg_dump -O --exclude-table-data='image*' --exclude-table-data='email*' -h componentdb.rds.aws.tid.al -U thehawk hawking > ~/tidal_component_dev.sql
5. Transfer tidal_component_dev.sql.gz to /opt/resources
6. Wipe out a local environment
7. Run make install locally
8. Run crawl/utility/pg_stagdump
9. Clean up chrom from pg_stagdump.sql
10. Run pg_stagdump.sql against production
11. Gzip and move images.dat and image_size.dat to /opt/resources
