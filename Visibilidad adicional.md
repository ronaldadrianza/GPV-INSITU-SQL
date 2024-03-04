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
  
 --- Extraccion de dimension cliente
  select
	codigo_cliente_tr_au as "Codigo Sucursal",
	descripcion as "Nombre Sucursal",
	banner_tipologia as "Banner / Tipologia",
	distrito as "Distrito",
	canal_principal as "Canal Principal"
from
	bk_exhibiciones_adicionales_nueva bean
	
--- Extraccion de dimension marca
	select
	distinct marca as "Marca" ,
	categoria as "Categoria" ,
	concat(marca,
	categoria) as "Llave marca"
from
	bk_exhibiciones_adicionales_nueva bean 
