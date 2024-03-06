 --- Extraccion tabla hechos de exhibiciones adicionales
    
  select 
  	codigo_cliente_tr_au as "Codigo Sucursal",
  	fecha_chequeo as "Fecha Chequeo",
  	initcap( usuario_chequeo) as "Usuario",
  	cedula_usuario_chequeo as "Cedula Usuario",
  	cargo as "Cargo Usuario",
  	codigo_actividad as "Codigo Actividad",
  	nombre_actividad as "Nombre Actividad",
  	tipo_negociacion as "Tipo de Negociación",
  	cast(cantidad as INTEGER) as "Cantidad",
  	foto1 as "Foto 1",
  	foto2 as "Foto 2",
  	foto3 as "Foto 3",
  	foto_adjunto as "Foto Adjunto",
  	CONCAT(marca, categoria) AS "Llave marca",
  	tipo_exhibicion as "Tipo Exhibicion",
  	nueva_en_mes as "Nueva"
  from bk_exhibiciones_adicionales_nueva bean 
  where fecha_chequeo >= '2022-01-01' and tipo_negociacion <> 'Planograma'
  order by fecha_chequeo desc
  

  
 --- Extraccion de dimension cliente maestro de SABANA y VISIBILIDAD ADICIONAL
select distinct 
    "codigo_cliente _tr_au_"  AS "Codigo Sucursal",
    descripcion AS "Nombre Sucursal",
    banner_tipologia AS "Banner / Tipologia",
    distrito AS "Distrito",
    CASE
        WHEN identificador = 'COOPIDROGAS' OR identificador = 'DROGUERIAS EN CADENA' THEN 'FARMACIAS'
        ELSE identificador
    END AS "Canal Unificado"
FROM
    "bk_FAMILIA_sabana_visibilidad_pactado" bfsvp 
WHERE
    "year" >= '2022'
 union  
      select distinct 
	codigo_cliente_tr_au as "Codigo Sucursal",
	descripcion as "Nombre Sucursal",
	banner_tipologia as "Banner / Tipologia",
	distrito as "Distrito",
	CASE
        WHEN banner_tipologia  in ('ALKOSTO', 'CARULLA', 'ARA', 'EXITO','CASH AND CARRY','DOLLAR CITY','HOMECENTER SODIMAC','JUMBO','MAKRO','METRO','OLIMPICA','SAO','PRICESMART COLOMBIA S.A.S',
        'SUPER TIENDA OLIMPICA','SUPERINTER','SURTIMAX','SURTIMAYORISTA') then 'CADENAS' 
        when banner_tipologia in ('ALMACENES EL CHISPAZO','ALMACENES PARAISO','ALTIPAL S.A', 'BELALCAZAR', 'CANASTA', 'CARIBE','COLSUBSIDIO CADENAS REGIONALES','COMERCIALIZADORA MONTES',
        'COMFANDI','CORBETA BOGOTA', 'CORBETA CALI', 'DESPENSA SAN AGUSTIN','DIRECTO', 'DISTRIBUIDORA TROPICANA','DISTRIBUIDORA TROPIQUINDIO','DISTRICOMER','EURO', 'LA QUINTA',
        'MAS POR MENOS', 'MEGATIENDAS','MERCACENTRO', 'MERCADO LIDER','MERCADO ZAPATOCA', 'MERCALDAS', 'MI FUTURO', 'PASADENA', 'POPULAR','PUYO','RAPIMERCAR','SUPER DE CADENA',
        'SUPER INDEPENDIENTES','SUPERTIENDAS CAÑAVERAL','SURTIFAMILIAR', 'SURTIPLAZA') then 'CR+SI' 
        when banner_tipologia in ('COLSUBSIDIO DROGUERIAS', 'DROGUERIA OLIMPICA','DROGUERIAS COOPIDROGUISTAS','DROGUERIAS DE DISTRIBUCION','DROGUERIAS EN CADENA', 'SUPER DROGUERIA OLIMPICA')
        then 'FARMACIAS'
        when banner_tipologia = 'MASCOTAS' then 'MASCOTAS'
        when banner_tipologia = 'PAÑALERAS' then 'PAÑALERAS'
        when banner_tipologia= 'MAYORISTAS' then 'MAYORISTAS'
        when banner_tipologia = 'CACHARRERIAS' then 'CACHARRERIAS'
        when banner_tipologia = 'MERCAFAM' then 'MERCAFAM'
        END AS "Canal Unificado"
from
	bk_exhibiciones_adicionales_nueva bean
	where fecha_chequeo >= '2022-01-01'
	
--- Extraccion de dimension marca
	select
	distinct marca as "Marca" ,
	categoria as "Categoria" ,
	concat(marca,
	categoria) as "Llave marca"
from
	bk_exhibiciones_adicionales_nueva bean 
	
  
 
  
  
 
