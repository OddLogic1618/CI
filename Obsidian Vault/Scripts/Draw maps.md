
```dataviewjs  
   let regionFolder = dv.pages('"Regions"');
   
   let drawByRegion = true;
   
   const lgWidth = 300;
   const lgHeight = 100;
   const gridUnits = 250
   const ea = ExcalidrawAutomate;
   

   function setConnectorSyle() {
     ea.style.strokeWidth = 15;
     ea.style.strokeColor = '#000000';
     ea.style.strokeStyle = 'solid'
     ea.style.roughness = 1;
     ea.style.startArrowHead = null;
     }

   function createObject(style, text, gridX, gridY) {
    let objWidth = 300;
    let objHeight = 100;
    let wrapat = 15;
    let boxt = true;
    let linkText = text
    switch (style) {
     case "Town Road":
      ea.style.strokeColor = '#000000';
      ea.style.backgroundColor = '#90918F';
      ea.style.fillStyle = 'solid';
      ea.style.strokeWidth = 3;
      ea.style.strokeSharpness = 'round';
      ea.style.strokeStyle = 'solid'
      ea.style.textAlign = 'center';
      ea.style.verticalAlign = 'middle';
      ea.style.fontSize = 15;
      ea.style.roughness = 2;
      boxt = true
      objWidth = 75;
      objHeight = 75;
      break;
     case "Road":
      ea.style.strokeColor = '#000000';
      ea.style.backgroundColor = 'Sienna';
      ea.style.fillStyle = 'solid';
      ea.style.strokeWidth = 3;
      ea.style.strokeSharpness = 'round';
      ea.style.strokeStyle = 'solid'
      ea.style.textAlign = 'center';
      ea.style.verticalAlign = 'middle';
      ea.style.fontSize = 15;
      ea.style.roughness = 2;
      boxt = true
      objWidth = 75;
      objHeight = 75;
      break;
     case "Shop":
      ea.style.strokeColor = '#6c3f19';
      ea.style.backgroundColor = 'bisque';
      ea.style.fillStyle = 'solid';
      ea.style.strokeWidth = 3;
      ea.style.strokeSharpness = 'sharp';
      ea.style.strokeStyle = 'solid'
      ea.style.textAlign = 'center';
      ea.style.verticalAlign = 'middle';
      ea.style.fontSize = 12;
      ea.style.roughness = 2;
      boxt = true
      objWidth = 33;
      objHeight = 33;
      wrapat = 15
      break;
     case "Region":
      ea.style.strokeColor = '#000000';
      ea.style.backgroundColor = 'DarkCyan';
      ea.style.fillStyle = "solid";
      ea.style.strokeWidth = 4;
      ea.style.strokeSharpness = 'sharp';
      ea.style.strokeStyle = 'dashed'
      ea.style.textAlign = 'center';
      ea.style.verticalAlign = 'top';
      ea.style.fontSize = 15;
      ea.style.roughness = 1;
      boxt = true
      objWidth = 75;
      objHeight = 75;
      wrapat = 18
       break;
     default:
      ea.style.strokeColor = '#000000';
      ea.style.backgroundColor = '#ff0000';
      ea.style.fillStyle = 'solid';
      ea.style.strokeWidth = 3;
      ea.style.strokeSharpness = 'sharp';
      ea.style.strokeStyle = 'dashed'
      ea.style.textAlign = 'center';
      ea.style.verticalAlign = 'middle';
      ea.style.fontSize = 15;
      ea.style.roughness = 2;
      boxt = true
      objWidth = 100;
      objHeight = 100;
      break;
    }
    let objWidthCenter = objWidth/2;
    let objHeightCenter = objHeight/2;
    
     return ea.addText((gridX * gridUnits) - objWidthCenter, (gridY * gridUnits) - objHeightCenter, linkText, {box:boxt, wrapAt:wrapat, width:objWidth, height:objHeight, textAlign: 'center'});
   }

   
   function zonePather(zonesAry, childZone, parentZone, parentSide) {
    let czType = childZone.type;
    let czName = "[[" + childZone.name + ".md|" + childZone.name + "]]";
    
    if (drawByRegion && parentZone) {
     if (String(parentZone.region) !== String(childZone.region)) {
      czType = "Region";
      let regName = dv.page(dv.parse(String(childZone.region))).file.name;
      czName = "[[" + regName + "-img.md|" + childZone.name + "\n in \n" + regName + "]]";
      }
	    
    }
    childZone["objectID"] = createObject(czType, czName, childZone.x, childZone.y);
    zonesAry[childZone.name] = childZone;
    let childSide;
    if (parentZone) {
     switch (parentSide) {
      case "top":
       childSide = "bottom";
       break;
      case "bottom":
       childSide = "top";
       break;
      case "right":
       childSide = "left";
       break;
      case "left":
       childSide = "right";
       break;
      }
     ea.style.strokeWidth = 15;
     ea.style.strokeColor = '#000000';
     ea.style.strokeStyle = 'solid'
     ea.style.roughness = 1;
     if (czType == "Region") ea.style.startArrowHead = 'arrow';
     else ea.style.startArrowHead = null;
     ea.connectObjects(childZone["objectID"], childSide, parentZone["objectID"], parentSide);
     }
    if (czType == "Region") return;
    let zone = dv.page(childZone.name);
    
    if (zone.north) {
     let z_str = String(zone.north);
     let newBorn = dv.page(dv.parse(z_str));
     if (newBorn) {
      let z = {
       "objectID": "",
       "name": newBorn.file.name,
       "type": newBorn.type,
       "region": newBorn.region,
       "x": childZone.x,
       "y": childZone.y - 1
      }
      if (newBorn.type == "Shop") z.y += 0.5
      if (!(newBorn.file.name in zonesAry)) zonePather(zonesAry, z, childZone, "top");
	  else {
       setConnectorSyle();
       ea.connectObjects(childZone["objectID"], "top", zonesAry[newBorn.file.name], "bottom");
      }
     }
     }
     
    if (zone.south) {
     let z_str = String(zone.south);
     let newBorn = dv.page(dv.parse(z_str));
     if (newBorn) {
      let z = {
       "objectID": "",
       "name": newBorn.file.name,
       "type": newBorn.type,
       "region": newBorn.region,
       "x": childZone.x,
       "y": childZone.y + 1
      }
      if (newBorn.type == "Shop") z.y -= 0.5
      if (!(newBorn.file.name in zonesAry)) zonePather(zonesAry, z, childZone, "bottom");
      else {
       setConnectorSyle();
       ea.connectObjects(childZone["objectID"], "bottom", zonesAry[newBorn.file.name], "top");
      }
     }
     }
     
    if (zone.east) {
     let z_str = String(zone.east);
     let newBorn = dv.page(dv.parse(z_str));
     if (newBorn) {
      let z = {
       "objectID": "",
       "name": newBorn.file.name,
       "type": newBorn.type,
       "region": newBorn.region,
       "x": childZone.x + 1,
       "y": childZone.y
      }
      if (newBorn.type == "Shop") z.x -= 0.5
      if (!(newBorn.file.name in zonesAry)) zonePather(zonesAry, z, childZone, "right");
      else {
       setConnectorSyle();
       ea.connectObjects(childZone["objectID"], "right", zonesAry[z.name]["objectID"], "left");
      }
     }
     }
     
    if (zone.west) {
     let z_str = String(zone.west);
     let newBorn = dv.page(dv.parse(z_str));
     if (newBorn) {
      let z = {
       "objectID": "",
       "name": newBorn.file.name,
       "type": newBorn.type,
       "region": newBorn.region,
       "x": childZone.x - 1,
       "y": childZone.y
      }
      if (newBorn.type == "Shop") z.x += 0.5
      if (!(newBorn.file.name in zonesAry)) zonePather(zonesAry, z, childZone, "left");
      else {
       setConnectorSyle();
       ea.connectObjects(childZone["objectID"], "right", zonesAry[newBorn.file.name], "left");
      }
     }
     }
   }

   function all_zones(rf){
    var zones = {};
    //var rootZ = {};
    //let rootZone ="";
    for (let re of rf){
     ea.reset();
     //ea.canvas.theme = 'dark';
	 ea.canvas.viewBackgroundColor = '#9c7555';
     ea.style.roughness = 2;
     ea.style.startArrowHead = 'none';
     ea.style.endArrowHead = 'none';
     //dv.list(re)
     //dv.header(1, re.file.name)
     //dv.span(re.file.path)
     //dv.span(dv.parse(String(re.root_zone)))
     //let rz_str = String(re.root_zone);
     let rootZone = dv.page(dv.parse(String(re.root_zone)));
     dv.span(rootZone.file.name)
     
     let rootZ = {
     "objectID": "",
     "name": rootZone.file.name,
     "type": rootZone.type,
     "region": rootZone.region,
     "x": 0,
     "y": 0
     }
     zones = {};
     //rootZ["objectID"] = createObject(rootZ.type, rootZ.name, rootZ.x, rootZ.y);
     zones[rootZone.file.name] = rootZ;
     zonePather(zones, rootZ);
     //ea.createSVG().then((svg)=>dv.span(svg.outerHTML));
     //let fName = '[[' + rootZone.region + ']]'
     let fName = re.file.name + "-img.md";
     ea.create({filename: String(fName),  onNewPane: false});
     
     }
    }
    
  all_zones(regionFolder)
```