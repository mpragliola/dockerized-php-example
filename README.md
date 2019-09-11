# Dockerized Nginx/PHP application

## Docker 

* **PHP version:** if you want to use a different PHP version, remember to update
  the requirements in `composer.json` accordingly

### Size optimizations

* Uses **Alpine** builds where possible to start with the smallest base imae
* Uses **multistage docker builds** to discard  any unneeded build leftovers
  (copying needed files from a `builder` to a `base` image)
* Leverages `apk` and his **virtual packages** to get rid of unneeded packages.
  As an example, after installing XDebug trough PECL you could gain up to ~200Mb
  more free space by deleting the build packages. 
   

### Docker-compose

* The repository uses Docker Compose version **3.5**, because it allows to specify
the target in **multistage builds**.

## 