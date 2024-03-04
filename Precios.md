--- Extraccion de la tabla de hechos precios nuestros
SELECT
	codigo_sucursal as "Codigo Sucursal",
	fecha_servidor as "Fecha Servidor" ,
	fecha_movil as "Fecha Movil",
	CAST(fecha_servidor  AS DATE) AS "Fecha llave",
	case WHEN EXTRACT(ISODOW FROM bpne.fecha_servidor) IN (6, 7) THEN 'Fin de semana' ELSE 'Semana' END AS "Semana o Fin de Semana",
	codigo_actividad as "Codigo Actividad",
	codigo_encuesta as "Codigo Encuesta",
	nombre_encuesta as "Nombre Encuesta" ,
	tipo_encuesta as "Tipo Encuesta" ,
	ean_producto as "Ean Producto",
	cast(cual_es_el_precio as DECIMAL(10,2)) as "Precio Chequeado" ,
	precio_con_descuento as "Precio con Descuento" ,
	precio_sugerido as "Precio Sugerido",
	foto as "Foto" ,
	observaciones as "Observaciones" 
FROM
	bk_precios_nueva_estructura_2023 bpne 
WHERE tipo_encuesta = 'PRECIOS' AND el_producto_se_encuentra_en_pdv = 'Si'
order BY fecha_servidor desc


--- Extraccion de la tabla de hechos precios competencias

SELECT
    codigo_sucursal as "Codigo Sucursal",
    fecha_servidor as "Fecha Servidor",
    fecha_movil as "Fecha Movil",
    CAST(fecha_servidor AS DATE) AS "Fecha llave",
    case WHEN EXTRACT(ISODOW FROM bpne.fecha_servidor) IN (6, 7) THEN 'Fin de semana' ELSE 'Semana' END AS "Semana o Fin de Semana",
    codigo_actividad as "Codigo Actividad",
    codigo_encuesta as "Codigo Encuesta",
    nombre_encuesta as "Nombre Encuesta",
    tipo_encuesta as "Tipo Encuesta",
    ean_producto as "Ean Competencia",
    CASE WHEN cual_es_el_precio <> '' THEN CAST(cual_es_el_precio AS DECIMAL(10, 2)) END as "Precio Competencia",
    ean_espejo_producto as "Ean Producto Familia",
    CASE WHEN precio_espejo <> '' THEN CAST(precio_espejo AS DECIMAL(10, 2)) END as "Precio Producto Familia",
    foto as "Foto",
    observaciones as "Observaciones"
FROM
    bk_precios_nueva_estructura_2023 bpne 
WHERE tipo_encuesta = 'COMPETENCIA' AND el_producto_se_encuentra_en_pdv = 'Si'
ORDER BY fecha_servidor DESC


--- Extraccion de dimension productos nuestros
SELECT DISTINCT
    fecha_servidor as "Fecha Servidor",
    ean_producto AS "Ean Producto",
    nombre_producto AS "Nombre Producto",
    UPPER(marca_producto) AS "Marca",
    UPPER(categoria_producto) AS "Categoria",
    UPPER(segmento_producto) AS "Segmento",
    CASE WHEN conteo_producto <> '' THEN CAST(conteo_producto AS DECIMAL(10, 2)) END AS "Conteo"
FROM
    bk_precios_nueva_estructura_2023 bpne
WHERE
    tipo_encuesta = 'PRECIOS' AND el_producto_se_encuentra_en_pdv = 'Si' AND conteo_producto <> ''
ORDER BY
    "Fecha Servidor" desc
  
--- Extraccion de dimension productos competencias
 SELECT DISTINCT
    fecha_servidor as "Fecha Servidor",
    ean_producto AS "Ean Producto",
    nombre_producto AS "Nombre Producto",
    UPPER(marca_producto) AS "Marca",
    UPPER(categoria_producto) AS "Categoria",
    UPPER(segmento_producto) AS "Segmento",
    CASE WHEN conteo_producto <> '' THEN CAST(conteo_producto AS DECIMAL(10, 2)) END AS "Conteo"
FROM
    bk_precios_nueva_estructura_2023 bpne
WHERE
    tipo_encuesta = 'COMPETENCIA' AND el_producto_se_encuentra_en_pdv = 'Si' AND conteo_producto <> ''
ORDER BY
    "Fecha Servidor" desc  
    
--- Extraccion de dimension cliente
select distinct 
	canal AS "Canal",
	distrito AS "Distrito",
	codigo_sucursal AS "Codigo Sucursal",
	nombre_sucursal AS "Nombre Sucursal" ,
	banner AS "Banner" ,
	deposito_distribuidor AS "Deposito Distribuidor" ,
	tipologia AS "Tipologia" ,
	canal_unificado AS "Canal Unificado"
from
	bk_precios_nueva_estructura_2023


