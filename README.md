# WordPress docker with custom WordPress implementation for AGILE

This project is a fork of [https://github.com/docker-library/wordpress](https://github.com/docker-library/wordpress) and uses a custom or the default WordPress implementations. Currently, we modified only php7.0/apache to use the WordPress implementation from [https://github.com/agile-iot/wordpress](https://github.com/agile-iot/wordpress). All other versions use the default WordPress container.
## Setup
Clone this repository:

    git clone https://github.com/agile-iot/wordpress-docker

To build and include the modified WordPress implementation with custom identity services, add this to the docker-compose file:

  sql-db:
    #In a rpi use this one 
    #image: hypriot/rpi-mysql
    #For intel use this one
    image: mysql:5.5
    container_name: sql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - 3306:3306/tcp
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      timeout: 20s
      retries: 10
    #this is needed to run the "script" and create the db for wso2is for testing.... comparison paper...
    #mysql -uroot -proot  -e "create database wso2is"
    #mysql -uroot -proot  -Dwso2is < /var/wso2is/script
    #volumes:
    #  - /home/dp/git/agile/agile-stack/apps/wso2is:/var/wso2is/




  agile-wordpress:
    build: 
      context: ./apps/wordpress
      dockerfile: php7.0/apache/Dockerfile
    container_name: agile-wordpress
    restart: always
    ports:
      - 80:80/tcp
    depends_on:
      sql-db:
        condition: service_healthy




Where ```sql-db``` is the database service, e. g. MySQL. 

Then add a symlink in your stack in the ```apps``` folder to the Dockerfile that uses the modified WordPress implementation.
For this, switch to ```$STACK/apps``` and add the symlink: ```ln -s /home/pi/wordpress-docker wordpress```, if you cloned this repository to /home/pi/.

## Build and start

After that you can build the container

    docker-compose build agile-wordpress

and start it

    sudo docker-compose up agile-wordpress
    
    
## Configuration

The WordPress code is at ```$DATA/wordpress```, where ```$DATA``` is your agile path, e. g. ```~/.agile```. In that directory you can find all WordPress files, including wp-config.php.
For the configuration of the modified WordPress implementation for different identity services, see [https://github.com/agile-iot/wordpress](https://github.com/agile-iot/wordpress)
