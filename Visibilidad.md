
--- Extraccion de tabla hechos de exhibiciones

select 
	codigo_cliente_au_tr  as "Codigo Sucursal",
	tipoexhibicion as "Tipo Exhibicion",
	fecha_chequeo as "Fecha chequeo",
	initcap( usuario_chequeo) as "Usuario",
	codigoactividad as "Codigo Actividad",
	cumple as "Cumple",
	case 
			when cumple = 'Cumple con exhibición y marca'
			or cumple = 'Cumple con otra exhibición y marca'
			or cumple = 'Cumple con exhibición y otra marca'
			or cumple = 'Cumple con otra exhibición' 
			or cumple = 'Cumple con otra exhibición y otra marca' then 'Cumple'
			when cumple ='No cumple' then 'No cumple'
			when cumple = ' ' then 'Sin chequear' end as "Agrupacion de cumplimiento",
	foto_adjunto as "Foto Adjunto",
	foto as "Foto",
	motivo as "Motivo",
	observaciones as "Observaciones",
	cual as "Cual",
	otra_marca as "Otra Marca",
	CONCAT(marca, categoria) as "Llave marca",
	CONCAT(origen_de_negociacion, tipo_de_origen, detalle) as "Llave negociacion",
	CONCAT(codigo_cliente_au_tr , extract(year from fecha_chequeo),extract(month from fecha_chequeo)) as "Llave padrino",
	CONCAT(tipoexhibicion,marca,categoria,codigo_cliente_au_tr,detalle,origen_de_negociacion,agrupador) as "Llave exhibiciones"
from
	bk_exhibiciones_nueva ben where  fecha_chequeo >= '2022-01-01'
	order by fecha_chequeo desc
	
--- Otra versión de extracción de la tb hechos de exhibiciones
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
    "Llave marca",
    "Llave negociacion",
    "Llave padrino",
    "Llave exhibiciones",
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
        CONCAT(marca, categoria) as "Llave marca",
        CONCAT(origen_de_negociacion, tipo_de_origen, detalle) as "Llave negociacion",
        CONCAT(codigo_cliente_au_tr, EXTRACT(YEAR FROM fecha_chequeo), EXTRACT(MONTH FROM fecha_chequeo)) as "Llave padrino",
        CONCAT(tipoexhibicion, marca, categoria, codigo_cliente_au_tr, detalle, origen_de_negociacion, agrupador) as "Llave exhibiciones",
        ROW_NUMBER() OVER (PARTITION BY CONCAT(tipoexhibicion, marca, categoria, codigo_cliente_au_tr, detalle, origen_de_negociacion, agrupador), extract(year from fecha_chequeo),extract(month from fecha_chequeo) ORDER BY fecha_chequeo DESC) as rn
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
    CONCAT(marca, categoria) AS "Llave marca",
    CONCAT(origen_de_negociacion, tipo_de_origen, detalle) AS "Llave negociacion",
    CONCAT("codigo_cliente _tr_au_", "year", "month") AS "Llave padrino",
    CONCAT(tipoexhibicion,marca,categoria,"codigo_cliente _tr_au_",detalle,origen_de_negociacion,agrupador) AS "Llave exhibiciones"
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
