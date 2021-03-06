<a href="https://play.google.com/store/apps/details?id=com.utymap.demo&utm_source=global_co&utm_medium=prtnr&utm_content=Mar2515&utm_campaign=PartBadge&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1"><img src="https://play.google.com/intl/en_us/badges/images/generic/en_badge_web_generic.png" alt="" height="64" /></a>

[![experimental](https://img.shields.io/badge/stability-experimental-orange.svg?style=flat)](https://github.com/reinterpretcat/utymap)
[![Build Status](https://travis-ci.org/reinterpretcat/utymap.svg?branch=master)](https://travis-ci.org/reinterpretcat/utymap)
[![Coverity](https://scan.coverity.com/projects/10159/badge.svg)](https://scan.coverity.com/projects/reinterpretcat-utymap)
[![Release](https://img.shields.io/github/release/reinterpretcat/utymap.svg)](https://github.com/reinterpretcat/utymap/releases/latest)
[![Join the chat at https://gitter.im/reinterpretcat/utymap](https://badges.gitter.im/reinterpretcat/utymap.svg)](https://gitter.im/reinterpretcat/utymap?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

<h2> Description </h2>

UtyMap is a library which provides highly customizable API for procedural world generation based on real vector map data, e.g. OpenStreetMap, NaturalEarth. Core logic is written on C++11 and can be used on many platforms as it has no dependency to specific game engine or application framework. It is designed for interactive world creation at different zoom levels, including globe and ground.

<h2> Features </h2>

<h3> Zoom levels </h3>
utymap can load your scene at different zoom levels:

<img src="https://cloud.githubusercontent.com/assets/1611077/19216383/4c10f3ee-8db9-11e6-8877-b652aa846734.png" />

<h3> Map data </h3>
utymap can use various vector map data sources and file formats:
<ul>
    <li>Mapzen (json)
    <li>OpenStreetMap (pbf, xml)
    <li>NaturalEarth (shape data)
</ul>

Theoretically, you can extend utymap with any vector map data format support.

<h3> Non flat world </h3>
utymap encapsulates elevation processing internally to support different elevation data providers:
<ul>
    <li>raw SRTM</li>
    <li>mapzen elevation data</li>
    <li>your own elevation data by conversion in simple text format.</li>
</ul>

[![Elevation:Prague](http://img.youtube.com/vi/mZzOWsoM5EY/0.jpg)](https://www.youtube.com/watch?v=mZzOWsoM5EY)

<h3> Styling </h3>
utymap supports styling via CSS-like language:

```CSS
area,relation|z16[building],
area,relation|z16[building:part] {
    builders: building;
    height: 12m;
    min-height: 0m;
    facade-color: gradient(#c0c0c0, #a9a9a9 50%, #808080);
    ...
}
area,relation|z16[roof:shape=skillion] {
    roof-type: skillion;
}
area,relation|z16[building:color] {
    roof-color: eval("tag('building:color')");
}
area,relation|z16[building:material=brick] {
    facade-color: gradient(#0fff96, #0cc775 50%, #066139);
    facade-texture-type: brick;
}
```
        
For example, you can have different styles for different seasons of year. Or you can vary textures based on region

<img src="https://cloud.githubusercontent.com/assets/1611077/19216355/3730d936-8db8-11e6-9c51-29b097b97bb7.png" />

<h3>Tree generation</h3>

utymap can consume map data to generate more complex tries via custom [L-System implementation](https://github.com/reinterpretcat/utymap/wiki/Tree-generation-via-L-System)
        
<img src="https://cloud.githubusercontent.com/assets/1611077/21949038/692a8b16-d9ef-11e6-9ff9-b03e5b052f73.png" />

<h3>Flexible extension</h3>

If you want to build or use your own models, extend application logic (for example, by attaching MonoBehaviour to generated meshes), you can do it using custom scripts in Unity:

```C#
public GameObject Build(Tile tile, Element element)
{
    GameObject gameObject = GameObject.CreatePrimitive(PrimitiveType.Cube);
    gameObject.name = GetName(element);
    
    var transform = gameObject.transform;
    transform.position = tile.Projection.Project(element.Geometry[0], 
        GetMinHeight(element) + element.Heights[0]);
    transform.localScale = new Vector3(2, 2, 2);

    gameObject.GetComponent<MeshFilter>().mesh.uv = GetUV(element);
    gameObject.GetComponent<MeshRenderer>().sharedMaterial = GetMaterial(element);

    return gameObject;
}
```

Map data is encapsulated via Element class which provides access to raw geometry and attributes.
        
<h3>Map editor</h3>

You can build your own objects (buildings, roads, etc.) on top of generated objects:

```C#
IElementEditor elementEditor = _compositionRoot.GetService<IElementEditor>();
var node = new Element(7,
    new GeoCoordinate[] { new GeoCoordinate(52.53182, 13.38762) },
    new double[] { 0 }, 
    new Dictionary<string, string>() { { "name", "Near me" } }, 
    new Dictionary<string, string>());

elementEditor.Add(MapStorageType.InMemory, node, levelOfDetails);
```
        
This data can be stored in multiple data storages located on disk or memory to keep original map data untouched.

<h2> Project structure </h2>
Project consists of two sub-projects:
<ul>
    <li><b>core</b> contains essential logic written on C++11 to build library for constructing of map based apps: terrain/buildings/osm-objects mesh generators, mapcss parser, spatial geo index, etc. It does not depend on any 3D render framework or game engine. </li>
    <li><b>unity</b> contains examples written on C# which can be reused to build map oriented Unity3D apps using core library. It demonstrates basic use cases: globe zoom level rendering, 3D scene with all details.</li>
</ul>

<h2> Install </h2>
See instructions [here] (https://github.com/reinterpretcat/utymap/wiki#install).
