# Instalación de APche+Visual Host:
- E mpezaremos paso por paso y para eso primero crearemos la red que vamos a utilizar usando el comando:

docker network create \
  --driver=bridge \
  --subnet=172.28.0.0/16 \
  --ip-range=172.28.5.0/24 \
  --gateway=172.28.5.254 \
  bind9_subnet

- Una vez hayamos hecho esto empezamos a configurar todos los archivos:

## Utiliza el repositorio en github del ejercicio anterior

- Utilizaremos los archivos del ejercicio anterior.

## Añade un DNS al docker-compose (puedes usar el que ya tienes)

servidor_dns:
    container_name: asir_Javiercitoserver_dns
    image: ubuntu/bind9
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      #Mapeo de puertos
    networks:
      xoela_subnet:
        ipv4_address: 162.22.5.10
    volumes:
      - ./configuracion:/etc/bind
      - ./zonas:/var/lib/bind
      #Para mapear los directorios

## Utiliza docker-compose para configurar las IP fijas a los dos contenedores
    
  - acuerdate de usar networks y nombre de la red para ponerla fija:
  - Aumque la ip sea diferente en cada contenedor el nombre deberá ser el mismo.
    
   + Para el Apache:

    networks:
      xoela_subnet:
        ipv4_address: 162.22.5.1
  
   + para el dns:

    networks:
        xoela_subnet:
          ipv4_address: 162.22.5.10

   + Pra el segundo contenedor del cliente usamos esta:

    networks:
      xoela_subnet:
        ipv4_address: 162.22.5.20

## El DNS tiene que resolver dos dominios a la ip del apache, por ejemplo:

- www.fabulasoscuras.com

  $TTL 38400	; 10 hours 40 minutes
  @		IN SOA	ns.fabulasmaravillosas.int. some.email.address. (
	  			10000002   ; serial
	  			10800      ; refresh (3 hours)
  				3600       ; retry (1 hour)
	  			604800     ; expire (1 week)
  				38400      ; minimum (10 hours 40 minutes)
  				)
  @		IN NS	ns.fabulasmaravillosas.int.
  ns		IN A		162.22.5.1
  test	IN A		162.22.5.4
  xoel    IN A        162.22.5.9
  www		IN CNAME	ns
  texto	IN TXT		mensaje

* www.fabulasmaravillosas.com

  $TTL 38400	; 10 hours 40 minutes
  @		IN SOA	ns.asircastelao.int. some.email.address. (
	  			10000002   ; serial
		  		10800      ; refresh (3 hours)
			  	3600       ; retry (1 hour)
	  			604800     ; expire (1 week)
		  		38400      ; minimum (10 hours 40 minutes)
			  	)
  @		IN NS	ns.asircastelao.int.
  ns		IN A		162.22.5.1
  test	IN A		162.22.5.4
  xoel    IN A   		162.22.5.9
  www	    IN CNAME	ns
  texto	IN TXT		mensaje

## Prueba a utilizar la directiva DirectoryIndex

<Directory "/usr/local/apache2/htdocs">
    
  - # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   AllowOverride FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>

## Configura dos virtual-host separados para cada dominio en el mismo puerto (80)

ServerName 162.22.5.1:80
ServerAdmin jfernandezdavila@danielcastelao.org
