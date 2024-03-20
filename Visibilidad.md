--- Union Sabana con Exhibiciones
SELECT
    s."codigo_cliente _tr_au_" AS "Codigo Sucursal",
    s.tipoexhibicion AS "Tipo Exhibicion",
    s.fecha_inicio AS "Fecha Inicio",
    s.fecha_fin AS "Fecha Fin",
    TO_DATE(CONCAT('1', '/', s."month", '/', s."year"), 'DD/MM/YYYY') AS "Fecha llave",
    CASE
        WHEN EXTRACT(MONTH FROM s.fecha_inicio) = EXTRACT(MONTH FROM s.fecha_fin) THEN 'Exhibiciones mes vencido'
        WHEN EXTRACT(MONTH FROM s.fecha_inicio) <> EXTRACT(MONTH FROM s.fecha_fin) AND DATE_PART('day', s.fecha_fin - s.fecha_inicio) > 30 THEN 'Exhibiciones mes vencido'
        WHEN EXTRACT(MONTH FROM s.fecha_inicio) <> EXTRACT(MONTH FROM s.fecha_fin) AND DATE_PART('day', s.fecha_fin - s.fecha_inicio) <= 30 THEN 'Exhibiciones mes a otro'
    END AS "Clasificación Negociación",
    s.agrupador AS "Agrupador",
    "year" as "Año",
    "month" as "Mes",
    CONCAT(s.marca, s.categoria) AS "Llave marca",
    CONCAT(s.origen_de_negociacion, s.tipo_de_origen, s.detalle) AS "Llave negociacion",
    CONCAT(s."codigo_cliente _tr_au_","year","month") as "Llave padrino",
    CONCAT(s.tipoexhibicion, s.marca, s.categoria, s."codigo_cliente _tr_au_", s.detalle, s.origen_de_negociacion, s.agrupador) AS "Llave exhibicion",
    e.codigo_cliente_au_tr AS "Codigo Sucursal (E)",
    e.fecha_chequeo AS "Fecha chequeo",
    e.usuario_chequeo AS "Usuario",
    e.codigoactividad AS "Codigo Actividad",
    e.cumple AS "Cumple",
    CASE
        WHEN e.cumple IN ('Cumple con exhibición y marca', 'Cumple con otra exhibición y marca', 'Cumple con exhibición y otra marca', 'Cumple con otra exhibición', 'Cumple con otra exhibición y otra marca') THEN 'Cumple'
        WHEN e.cumple ='No cumple' THEN 'No cumple'
        WHEN e.cumple = ' ' THEN 'Sin chequear'
    END AS "Agrupacion de cumplimiento",
    e.foto_adjunto AS "Foto Adjunto",
    e.foto AS "Foto",
    e.motivo AS "Motivo",
    e.observaciones AS "Observaciones",
    e.cual AS "Cual",
    e.otra_marca AS "Otra Marca",
    e.agrupador AS "Agrupador (E)",
    CONCAT(e.tipoexhibicion, e.marca, e.categoria, e.codigo_cliente_au_tr, e.detalle, e.origen_de_negociacion, e.agrupador) AS "Llave chequeos"
FROM
    "bk_FAMILIA_sabana_visibilidad_pactado" s
LEFT JOIN
    "bk_exhibiciones_nueva" e
ON
    replace(CONCAT(s.tipoexhibicion, s.marca, s.categoria, s."codigo_cliente _tr_au_", s.detalle, s.origen_de_negociacion, "year","month", s.agrupador),' ','')
    =
    replace(CONCAT(e.tipoexhibicion, e.marca, e.categoria, e.codigo_cliente_au_tr, e.detalle, e.origen_de_negociacion,
    EXTRACT(YEAR FROM e.fecha_chequeo), EXTRACT(MONTH FROM e.fecha_chequeo), e.agrupador),' ','')
    where "year" in ('2022','2023','2024')




--- Extraccion de tabla hechos de exhibiciones
SELECT 
    "Codigo Sucursal",
    "Tipo Exhibicion",
    "Fecha chequeo",
    "Usuario",
    "Codigo Actividad",
    "Cumple",
    "Agrupacion de cumplimiento",
    "Foto Adjunto",
    "Foto",
    "Motivo",
    "Observaciones",
    "Cual",
    "Otra Marca",
    "Agrupador",
    "Llave marca",
    "Llave negociacion",
    "Llave padrino",
    "Unión - Negociación",
    "Llave chequeos",
    CASE 
        WHEN rn = 1 THEN 'Último Chequeo'
        WHEN rn > 1 THEN 'Chequeo Anterior'
    END AS "Tipo Chequeo"
FROM 
     (
    SELECT 
        codigo_cliente_au_tr  as "Codigo Sucursal",
        tipoexhibicion as "Tipo Exhibicion",
        fecha_chequeo as "Fecha chequeo",
        usuario_chequeo as "Usuario",
        codigoactividad as "Codigo Actividad",
        cumple as "Cumple",
        CASE 
            WHEN cumple IN ('Cumple con exhibición y marca', 'Cumple con otra exhibición y marca', 'Cumple con exhibición y otra marca', 'Cumple con otra exhibición', 'Cumple con otra exhibición y otra marca') THEN 'Cumple'
            WHEN cumple ='No cumple' THEN 'No cumple'
            WHEN cumple = ' ' THEN 'Sin chequear' 
        END as "Agrupacion de cumplimiento",
        foto_adjunto as "Foto Adjunto",
        foto as "Foto",
        motivo as "Motivo",
        observaciones as "Observaciones",
        cual as "Cual",
        otra_marca as "Otra Marca",
        agrupador as "Agrupador",
        CONCAT(marca, categoria) as "Llave marca",
        CONCAT(origen_de_negociacion, tipo_de_origen, detalle) as "Llave negociacion",
        CONCAT(codigo_cliente_au_tr, EXTRACT(YEAR FROM fecha_chequeo), EXTRACT(MONTH FROM fecha_chequeo)) as "Llave padrino",
        CONCAT(tipoexhibicion, marca, categoria, codigo_cliente_au_tr, detalle, origen_de_negociacion,extract(year from fecha_chequeo),extract(month from fecha_chequeo), agrupador) as "Unión - Negociación",
        CONCAT(tipoexhibicion, marca, categoria, codigo_cliente_au_tr, detalle, origen_de_negociacion,agrupador) as "Llave chequeos",
        ROW_NUMBER() OVER (PARTITION BY CONCAT(tipoexhibicion, marca, categoria, codigo_cliente_au_tr, detalle, origen_de_negociacion, agrupador),
        extract(year from fecha_chequeo),extract(month from fecha_chequeo) ORDER BY fecha_chequeo DESC) as rn
    FROM
        bk_exhibiciones_nueva ben 
    WHERE 
        fecha_chequeo >= '2022-01-01'
) as temptable

	
	
--- Extraccion de tabla hechos de sabana	
select
    "codigo_cliente _tr_au_" AS "Codigo Sucursal",
    tipoexhibicion AS "Tipo Exhibicion",
    fecha_inicio AS "Fecha Inicio",
    fecha_fin AS "Fecha Fin",
    TO_DATE(CONCAT('1', '/', "month", '/', "year"), 'DD/MM/YYYY') AS "Fecha llave",
    CASE
        WHEN EXTRACT(MONTH FROM fecha_inicio) = EXTRACT(MONTH FROM fecha_fin) THEN 'Exhibiciones mes vencido'
        WHEN EXTRACT(MONTH FROM fecha_inicio) <> EXTRACT(MONTH FROM fecha_fin) AND DATE_PART('day', fecha_fin - fecha_inicio) > 30 THEN 'Exhibiciones mes vencido'
        WHEN EXTRACT(MONTH FROM fecha_inicio) <> EXTRACT(MONTH FROM fecha_fin) AND DATE_PART('day', fecha_fin - fecha_inicio) <= 30 THEN 'Exhibiciones mes a otro'
    END AS "Clasificación Negociación",
    agrupador as "Agrupador",
    CONCAT(marca, categoria) AS "Llave marca",
    CONCAT(origen_de_negociacion, tipo_de_origen, detalle) AS "Llave negociacion",
    CONCAT("codigo_cliente _tr_au_", "year", "month") AS "Llave padrino",
    CONCAT(tipoexhibicion,marca,categoria,"codigo_cliente _tr_au_",detalle,origen_de_negociacion,"year","month",agrupador) AS "Unión - Chequeos",
    CONCAT(tipoexhibicion,marca,categoria,"codigo_cliente _tr_au_",detalle,origen_de_negociacion,agrupador) AS "Llave exhibicion"
FROM
    "bk_FAMILIA_sabana_visibilidad_pactado" bfsvp
WHERE
    "year" IN ('2022', '2023', '2024')
 order by "year" desc
    

	
--- Extraccion de dimension marca
select
	distinct marca as "Marca" ,
	categoria as "Categoria" ,
	concat(marca,
	categoria) as "Llave marca"
from
	bk_exhibiciones_nueva ben

--- Extraccion de dimension negociacion
select distinct 
	origen_de_negociacion as "Origen de Negociació" ,
	tipo_de_origen as "Tipo de Origen",
	detalle as "Detalle",
	concat(origen_de_negociacion,
	tipo_de_origen,
	detalle) as "Llave negociacion"
from
	bk_exhibiciones_nueva ben

--- Extracción de dimension cliente
SELECT
    canal AS "Canal",
    cliente_corporativo AS "Cliente Corporativo",
    grupo_detallista AS "Grupo Detallista",
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

     --- Extraccion tabla hechos de exhibiciones adicionales
    
  select 
  	codigo_cliente_tr_au as "Codigo Sucursal",
  	fecha_chequeo as "Fecha Chequeo",
  	initcap( usuario_chequeo) as "Usuario",
  	cedula_usuario_chequeo as "Cedula Usuario",
  	cargo as "Cargo Usuario",
  	codigo_actividad as "Codigo Actividad",
  	dinamicas as "Dinamicas",
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
