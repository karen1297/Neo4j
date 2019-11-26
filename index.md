## Ejemplo de Fraude en Comercios

## Clientes
CREATE (Fernando:Persona {id:'1', nombre:'Fernando', sexo:'masculino', edad:'50'})
CREATE (Juan:Persona {id:'2', nombre:'Juan', sexo:'masculino', edad:'48'})
CREATE (Daniel:Persona {id:'3', nombre:'Daniel', sexo:'masculino', edad:'23'})
CREATE (Marcos:Persona {id:'4', nombre:'Marcos', sexo:'masculino', edad:'30'})
CREATE (Gonzalo:Persona {id:'5', nombre:'Gonzalo', sexo:'masculino', edad:'31'})
CREATE (Marta:Persona {id:'6', nombre:'Marta', sexo:'femenino', edad:'52'})

## Comercios
CREATE (Fnac:Comercio {id:'11', nombre:'Fnac', calle:'2626 Wilkinson Court', address:'Madrid 92410'})
CREATE (El_Corte_Ingles:Comercio {id:'12', nombre:'El Corte Ingles', calle:'Nuevos Minist', address:'Madrid 92410'})
CREATE (Primark:Comercio {id:'13', nombre:'Primark', calle:'2092 Larry Street', address:'Madrid 92410'})
CREATE (MacDonalds:Comercio {id:'14', nombre:'MacDonalds', calle:'1870 Caynor Circle', address:'Madrid 92410'})
CREATE (Springfield:Comercio {id:'15', nombre:'Springfield', calle:'1381 Spruce Drive', address:'Madrid 92410'})
CREATE (Burguer_King:Comercio {id:'16', nombre:'Burguer King', calle:'826 Anmoore Road', address:'Madrid 92410'})
CREATE (Ikea:Comercio {id:'17', nombre:'Ikea', calle:'1925 Spring Street', address:'Madrid 92410'})
CREATE (Nike:Comercio {id:'18', nombre:'Nike', calle:'4209 Elsie Drive', address:'Madrid 92410'})
CREATE (Adidas:Comercio {id:'19', nombre:'Adidas', calle:'86 D Street', address:'Madrid 92410'})
CREATE (Sprinter:Comercio {id:'20', nombre:'Sprinter', calle:'945 Kinney Street', address:'Madrid 92410'})
CREATE (Starbucks:Comercio {id:'21', nombre:'Starbucks', calle:'3810 Apple Lane', address:'Madrid 92410'})

## Compras
CREATE (Fernando)-[:HA_COMPRADO_EN {cantidad:'986', fecha:'4/17/2015', estado:'Legitima'}]->(Burguer_King)
CREATE (Fernando)-[:HA_COMPRADO_EN {cantidad:'239', fecha:'5/15/2015', estado:'Legitima'}]->(Starbucks)
CREATE (Fernando)-[:HA_COMPRADO_EN {cantidad:'475', fecha:'3/28/2015', estado:'Legitima'}]->(Nike)
CREATE (Fernando)-[:HA_COMPRADO_EN {cantidad:'654', fecha:'3/20/2015', estado:'Legitima'}]->(Primark)
CREATE (Juan)-[:HA_COMPRADO_EN {cantidad:'196', fecha:'7/24/2015', estado:'Legitima'}]->(Adidas)
CREATE (Juan)-[:HA_COMPRADO_EN {cantidad:'502', fecha:'4/9/2015', estado:'Legitima'}]->(El_Corte_Ingles)
CREATE (Juan)-[:HA_COMPRADO_EN {cantidad:'848', fecha:'5/29/2015', estado:'Legitima'}]->(Primark)
CREATE (Juan)-[:HA_COMPRADO_EN {cantidad:'802', fecha:'3/11/2015', estado:'Legitima'}]->(Fnac)
CREATE (Juan)-[:HA_COMPRADO_EN {cantidad:'203', fecha:'3/27/2015', estado:'Legitima'}]->(Subway)
CREATE (Daniel)-[:HA_COMPRADO_EN {cantidad:'35', fecha:'1/23/2015', estado:'Legitima'}]->(MacDonalds)

## Consultas
1. Operaciones Fraudelentas
MATCH (victima:Persona)-[r:HA_COMPRADO_EN]->(comercio)
WHERE r.estado = "Fraudulenta"RETURN 
victima.nombre AS `Nombre Cliente`, 
comercio.nombre AS `Nombre Comercio`, 
r.cantidad AS Cantidad, 
r.fecha AS `Fecha Transaccion`
ORDER BY `Fecha Transaccion` DESC

2. Comercios con Fraude
MATCH (victima:Persona)-[r:HA_COMPRADO_EN]->(comercio)
WHERE r.estado = "Fraudulenta"
MATCH (victima)-[t:HA_COMPRADO_EN]->(otroscomercios)
WHERE t.estado = "Legitima"AND t.fecha < r.fecha
WITH victima, otroscomercios, t 
ORDER BY t.fecha DESC
RETURN 
victima.nombre AS `Nombre Cliente`, 
otroscomercios.nombre AS `Nombre Comercio`, 
t.cantidad AS Cantidad, 
t.fecha AS `Fecha Transaccion`
ORDER BY `Fecha Transaccion` DESC

3. No. de Personas comprando en los Comercios
MATCH (victima:Persona)-[r:HA_COMPRADO_EN]->(comercio)
WHERE r.estado = "Fraudulenta"
MATCH (victima)-[t:HA_COMPRADO_EN]->(otroscomercios)
WHERE t.estado = "Legitima"AND t.fecha < r.fecha
WITH victima, otroscomercios, t ORDER BY t.fecha DESC
RETURN
DISTINCT otroscomercios.nombre AS `Comercio Sospechoso`,
count(DISTINCT t) AS Contador,
collect(DISTINCT victima.nombre) AS Victimas
ORDER BY Contador DESC
