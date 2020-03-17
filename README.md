# Alap LAMP környezet dockerben

Több konfiguráció is kezelhető ebben a beállításban. Alapértelmezetten csak a munkaállomáson használatos `local` környezet van definiálva, azonban létrehozhatunk production, staging, testing, stb. konfigurációt is, annak függvényében hogy hányra van szükségünk. Ezek létrehozásához használhatjuk sablonként a `local` környezetet.

Ezek a konfigurációk a `/bin/docker` könyvtár alatt találhatóak és mindegyik rendelkezik egy különálló könyvtárral. A továbbiakban a fájlnév hivatkozások az adott környezet könyvtárából kiindulva értendő (pl. `/nginx/config/site.template` a valóságban `/bin/docker/local/nginx/config/site.template`-et jelent local környezet esetén).

## Konfiguráció

A környezet a .env fájl szerkesztésével konfigurálható. Jelenleg az alábbi beállítási lehetőségek érhetőek el:

`PROJECT_NAME` - Projekt slug neve, amely a konténerek nevében van felhasználva prefixként  

`NGINX_VERSION` - Nginx kiszolgáló verziója  
`NGINX_PORT` - A host gépen használt port az nginx kiszolgálóhoz  
`NGINX_HOST` - A kiszolgált domain név

`MYSQL_VERSION` - Futtatandó MySQL verzió  
`MYSQL_PORT` - A host gépen használt port a DB-hez csatlakozáshoz  
`MYSQL_DATABASE` - Adatbázis neve  
`MYSQL_USER` - Adatbázishoz tartozó felhasználó neve  
`MYSQL_PASSWORD` - Felhasználó jelszava  
`MYSQL_ROOT_PASSWORD` - root felhasználó jelszava  

`PHP_VERSION` - A projektben használatos PHP verzió száma  
`PHP_XDEBUG_HOST` - XDebug host name  
`PHP_XDEBUG_PORT` - XDebug port  
`PHP_XDEBUG_IDEKEY` - XDebug IDE key  

## Linux

Alapból alpine linux alapú konténereket használ a környezet. Ez egy minimalista disztribúció, ezért kevés erőforrással is beéri.

A konténerek ebből kifolyólag nem tartalmaznak bash parancshéjat, helyette az ash használható:

`docker exec -ti test-nginx ash`

## Webszerver

Nginx van használatban webszerverként.

Az nginx configja az `/nginx/config/site.template` sablonból generálódik ki az nginx container környezeti változói alapján.

A webszerver logjai az `/nginx/log` könyvtárban találhatóak.

## Adatbázis

A környezet jelenleg MySQL szervert használ adatbázisként. Az adatbázis fájljai a `/mysql/data` könyvtárban tárolódnak, így újraindítás vagy kód hordozása során is megmaradnak a tárolt adatok.

## PHP

Létrehozható egyedi konfigurációs fájl a PHP környezethez a `/php/config` könyvtárba létrehozott fájllal. Ennek a fájlnak a neve tetszőleges, az egyetlen megkötés hogy .ini-nek kell lennie. Az ebben a könyvtárban lévő fájlokat a PHP mind beolvassa és a benne található beállításokkal felülírja az alapértelmezett beállításokat.

A PHP containerben telepítésre kerülnek a `pdo pdo_mysql gd zip` PHP libek, a composer, valamint beállításra került az XDebug.