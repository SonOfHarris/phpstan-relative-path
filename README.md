# PHPStan relative path cache invalidation

Steps to demonstrate that optional paths (defined via `(?)`) are stored as an absolute path in the cache.
If any optional paths are included then the cache is always invalidated when there is a change to the anchor directory.


## Build the cache

1. Clone repository

2. Install composer dependencies:

       composer install

3. Run phpstan:

       vendor/bin/phpstan analyse -vv
       # Result cache not used because the cache file does not exist.

4. Check the contents of the `.cache/resultCache.php`

> The `src/Optional` and `src/Ignored` entries will be absolute rather than relative.

## Invalidate the cache

1. Rename the repository folder to anything else

2. Run phpstan:
       
       vendor/bin/phpstan analyse -vv
       # Result cache not used because the metadata do not match: projectConfig

4. Check the contents of the `.cache/resultCache.php`

> The `src/Optional` and `src/Ignored` entries will have a different absolute path which caused the cache to be invalidated.

## Using required paths

1. Replace the configuration file with the required version:

       cp phpstan.required.neon phpstan.neon

2. Run phpstan:

       vendor/bin/phpstan analyse -vv
       # Result cache not used because the metadata do not match: projectConfig, scannedFiles

3. Check the contents of the `.cache/resultCache.php` file:

> The `src/Optional` and `src/Ignored` entries will now be relative.

4. Rename the repository folder again to anything else

5. Run phpstan:

       vendor/bin/phpstan analyse -vv
       # Result cache restored. 0 files will be reanalysed.

> The cache is not invalidated the paths have remained relative.
   