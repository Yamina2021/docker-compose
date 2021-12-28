# docker-compose

how to use Docker to containerize applications

## links 
http://localhost:8083/                   ==>Adminer
http://localhost:8082/core/install.php   ==>drupal
http://localhost:8081/                   ==>wordpress

##docker-compose##

version: '3.7'

services:
  nginx-proxy:
        container_name: nginx-proxy
        ports:
            - '80:80'
            - '443:443'
        volumes:
            - '/var/run/docker.sock:/tmp/docker.sock:ro'
        restart: always
        image: jwilder/nginx-proxy
    
  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8081:80
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: admin
      WORDPRESS_DB_PASSWORD: admin
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - default

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
    volumes:
      - db:/var/lib/mysql
      - ./init:/docker-entrypoint-initdb.d
    networks:
      - default

  adminer:
    image: adminer
    restart: always
    ports:
      - 8083:8080
    networks:
      - default
    
  drupal:
    image: drupal:9.3
    ports:
      - 8082:80
    environment:
      DRUPAL_DB_HOST: db:3306
      DRUPAL_DB_USER: admin
      DRUPAL_DB_PASSWORD: admin
      DRUPAL_DB_NAME: drupal
    volumes:
      - drupal_modules:/var/www/html/modules
      - drupal_profiles:/var/www/html/profiles
      - drupal_themes:/var/www/html/themes
      - drupal_sites:/var/www/html/sites
    restart: always
    networks:
      - default


volumes:
  wordpress:
  drupal_modules:
  drupal_profiles:
  drupal_themes:
  drupal_sites:
  db:

networks:
 default:
