#### Harbor update 

I meet many other problems when I decide update my harbor service. 

- with admin password
- with datamigration 
- with certificate parameters 

first of all you must undestand that you can't update on with any version to any version 
you must update on every version throught your path^ for examle 
If you have version 1.8.1 you and you want update to version 2.7.0, you must update to 1.10.0 next 2.0.0 next 2.2.0 next 2.3.0 .. ... 2.6.0 annd the end 2.7.0


#### Updating to 1.10.0 

    cp -r harbor /root/
    cp -R /data/database /root/
    mv harbor harbor_1.8.1
    wget https://github.com/goharbor/harbor/releases/download/v1.10.0/harbor-offline-installer-v1.10.0.tgz
    tar zxf harbor-offline-installer-v1.10.0.tgz 
    docker image load -i harbor/harbor.v1.10.0.tar.gz 
    docker run -it --rm -v /opt/harbor_1.8.1/harbor.yml:/harbor-migration/harbor-cfg/harbor.yml goharbor/harbor-migrator:v1.10.0 --cfg up
    mv harbor/harbor.yml harbor/harbor.yml_back
    cp harbor_1.8.1/harbor.yml  harbor/harbor.yml 

Attention, you must use real path NOT SYMLINK

    ls -la ${PATH_TO_YOUR_CERTIFICATE}

Example

    ls -la /etc/letsencrypt/archive/example.com/fullchain.pem
    ls -la /etc/letsencrypt/archive/example.com/privkey.pem

    nano harbor/harbor.yml
    ``` paste faullchain and privkey path```

    ./harbor/install.sh 
 


##### Skip  admin password to database

    docker exec -it harbor-db bash
    psql -U postgres -d registry
    select * from harbor_user;
    update harbor_user set salt='', password='' where user_id = 1;


#### Updating to 2.0.0 and next version

Download firmware

    wget https://github.com/goharbor/harbor/releases/download/v[VERSION]/harbor-offline-installer-v[VERSION].tgz

example

    wget https://github.com/goharbor/harbor/releases/download/v2.0.0/harbor-offline-installer-v2.0.0.tgz

Extract 

    tar zxf harbor-offline-installer-v[VERSION].tgz 

example

    tar zxf harbor-offline-installer-v2.0.0.tgz 

Build docker

    docker image load -i harbor/harbor.v[VERSION].tar.gz 

example

     docker image load -i harbor/harbor.v2.0.0.tar.gz 

migrate data

    docker run -it --rm -v /:/hostfs goharbor/prepare:[VERSION] migrate -i ${path to harbor.yml}

example

    docker run -it --rm -v /:/hostfs goharbor/prepare:2.0.0 migrate -i ${path to harbor.yml}
    ./harbor/install.sh 