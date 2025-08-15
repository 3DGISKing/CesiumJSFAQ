# CesiumJS FAQ

## General

### How to get a Cartographic from a Cartesian3?

```
const cartographic = Cesium.Cartographic.fromCartesian(cartesian);
```

```
const ellipsoid = viewer.scene.globe.ellipsoid;
const cartographic = ellipsoid.cartesianToCartographic(cartesian);
```

### How to get a Cartesian3 from a Cartographic?

```
const cartesian = scene.globe.ellipsoid.cartographicToCartesian(cartographic);
```

```
const cartesian = Cesium.Ellipsoid.WGS84.cartographicToCartesian(cartographic);
```

```
const cartesian = Cesium.Cartesian3.fromRadians(cartographic.longitude, cartographic.latitude, cartographic.height);
```

### How to convert a 3D world coordinate to a 2D window (screen) position?

```
const screenPosition = Cesium.SceneTransforms.worldToWindowCoordinates(scene, positionWorld, new Cesium.Cartesian2());
```

```
const screenPosition = scene.cartesianToCanvasCoordinates(positionWorld, new Cesium.Cartesian2());
```

### How to get 3D Cartesian coordinates from 2D window coordinates?

```
const cartesian3D = viewer.scene.pickPosition(windowPosition);
```

## Math

### How to draw ECEF?

```
const axisLength = 15000000;

const xAxisEnd = new Cesium.Cartesian3(axisLength, 0, 0);
const yAxisEnd = new Cesium.Cartesian3(0, axisLength, 0);
const zAxisEnd = new Cesium.Cartesian3(0, 0, axisLength);

viewer.entities.add({
    polyline: {
    positions: [new Cesium.Cartesian3(1, 0, 0), xAxisEnd],
    width: 10,
    arcType: Cesium.ArcType.NONE,
    material: new Cesium.PolylineArrowMaterialProperty(
        Cesium.Color.RED
    ),
    },
});

viewer.entities.add({
    polyline: {
    positions: [new Cesium.Cartesian3(1, 0, 0), yAxisEnd],
    width: 10,
    arcType: Cesium.ArcType.NONE,
    material: new Cesium.PolylineArrowMaterialProperty(
        Cesium.Color.YELLOW
    ),
    },
});

viewer.entities.add({
    polyline: {
    positions: [new Cesium.Cartesian3(1, 0, 0), zAxisEnd],
    width: 10,
    arcType: Cesium.ArcType.NONE,
    material: new Cesium.PolylineArrowMaterialProperty(
        Cesium.Color.BLUE
    ),
    },
});
```

### How to convert an angle from radians to degrees?

```
const degrees = Cesium.Math.toDegrees(radians);
```

### How to get the center between two Cartesian3 positions?

```
const center = Cartesian3.midpoint(start, end, new Cesium.Cartesian3());
```

```
const center = Cesium.Cartesian3.add(start, end, new Cesium.Cartesian3());
Cesium.Cartesian3.multiplyByScalar(center, 0.5, center);
```

### How to get the centroid of a set of points?

```
// positions is an array of Cesium.Cartesian3 points.
const center = Cesium.BoundingSphere.fromPoints(positions).center;
```

### How to get the angle in radians between the two Cartesian3 vectors?

```
const angleBetween = Cesium.Cartesian3.angleBetween(p0, p1);
```

### How to convert a local point to a world point?

```
const hpr = new Cesium.HeadingPitchRoll(heading, pitch, roll);
const matrix = Cesium.Transforms.headingPitchRollToFixedFrame(origin, hpr);
const localPosition = new Cesium.Cartesian3(0, 0, 1);
const worldPosition = Cesium.Matrix4.multiplyByPoint(matrix, localPosition, new Cesium.Cartesian3());
```

### How to convert a world point to a local point?

```
const invTransform = Cesium.Matrix4.inverseTransformation(transform, new Cesium.Matrix4());
const localPosition = Cesium.Matrix4.multiplyByPoint(invTransform, worldPosition, new Cesium.Cartesian3());
```

### How to get the center (translation) and heading from a Matrix4?

```
const center = Cesium.Matrix4.getTranslation(matrix, new Cesium.Cartesian3());
const hpr = Cesium.Transforms.fixedFrameToHeadingPitchRoll(matrix);
const headingDegrees = Cesium.Math.toDegrees(hpr.heading);
```

### How to determine the east, north, up direction at a given position?

```
const enuMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(position);

const east = new Cesium.Cartesian3(enuMatrix[4], enuMatrix[5], enuMatrix[6]);
const north = new Cesium.Cartesian3(enuMatrix[8], enuMatrix[9], enuMatrix[10]);
const up = new Cesium.Cartesian3(enuMatrix[12], enuMatrix[13], enuMatrix[14]);
```

### How to calculate geodesic distance?

```
const west = -115, east = -114, north = 38, south = 37;

const westNorth = Cesium.Cartographic.fromDegrees(west, north);
const eastNorth = Cesium.Cartographic.fromDegrees(east, north);
const westSouth = Cesium.Cartographic.fromDegrees(west, south);

const westEastGeodesic = new Cesium.EllipsoidGeodesic(westNorth, eastNorth);
const southNorthGeodesic = new Cesium.EllipsoidGeodesic(westSouth, westNorth);

const width = westEastGeodesic.surfaceDistance;
const height = southNorthGeodesic.surfaceDistance;
```

### How to calculate pixel size of bounding sphere?

```
const context = viewer.scene.context;

const maxPixelSize = Math.max(context.drawingBufferWidth, context.drawingBufferHeight);
const metersPerPixel = camera.getPixelSize(this._boundingSphere, context.drawingBufferWidth, context.drawingBufferHeight);

const radius = this._boundingSphere.radius;
const pixelsPerMeter = 1.0 / metersPerPixel;
const diameterInPixels = Math.min(pixelsPerMeter * (2.0 * radius), maxPixelSize);
```

## Viewer

### How to hide all tool buttons?

```
const viewer = new Viewer("cesiumContainer", {
    baseLayerPicker: false,
    homeButton: false,
    sceneModePicker: false,
    navigationHelpButton: false,
    geocoder: false
});
```

### How to hide the fullscreen button?

```
viewer.fullscreenButton.container.style.display = "none";
```

### How to hide ion credit?

```
viewer.bottomContainer.style.display = "none";
```

### How to hide timeline?

```
const viewer = new Cesium.Viewer("cesiumContainer", {
    timeline: false
});
```

### How to hide animation widget?

```
const viewer = new Cesium.Viewer("cesiumContainer", {
    animation: false,
});
```

### how to disable user input?

```
scene.screenSpaceCameraController.enableRotate = false;
scene.screenSpaceCameraController.enableTranslate = false;
scene.screenSpaceCameraController.enableZoom = false;
scene.screenSpaceCameraController.enableTilt = false;
scene.screenSpaceCameraController.enableLook = false;
```

## Camera

### How to set default view?

```
Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
    73.6753792663,
    18.197700914,
    135.026311477,
    53.4588044297
);
Cesium.Camera.DEFAULT_VIEW_FACTOR = 0;
```

### How to enable underground navigation?

```
viewer.scene.screenSpaceCameraController.enableCollisionDetection = false;
```

### How to check if the camera is underground?

```
viewer.scene.cameraUnderground;
```

### How to listen Camera change event?

```
viewer.camera.percentageChanged = 0.001;
viewer.camera.changed.addEventListener(function () {
    console.log('camera changed')
});

```

### Entity API

### Point

```
const citizensBankPark = viewer.entities.add({
    name : 'Citizens Bank Park',
    position : Cesium.Cartesian3.fromDegrees(-75.166493, 39.9060534),
    point : {
        pixelSize : 5,
        color : Cesium.Color.RED,
        outlineColor : Cesium.Color.WHITE,
        outlineWidth : 2
    }
});
```

### Billboard

```
viewer.entities.add({
    position: Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883),
    billboard: {
    image: "../images/whiteShapes.png",
    imageSubRegion: new Cesium.BoundingRectangle(49, 43, 18, 18),
    color: Cesium.Color.LIME,
    }
});

```

### Label

```
viewer.entities.add({
    position: Cesium.Cartesian3.fromDegrees(this._longitude, this._latitude, 11),
    label: {
        text: “label”,
        scale: 0.8,
        pixelOffset: new Cesium.Cartesian2(0, -30),
        font: "32px Helvetica",
        fillColor: Cesium.Color.YELLOW,
        outlineColor: Cesium.Color.BLACK,
        outlineWidth: 2,
        style: Cesium.LabelStyle.FILL_AND_OUTLINE,
    }
});

```

### Polyline

```
viewer.entities.add({
    polyline : {
        positions : [new Cartesian3(-2358138.847340281, -3744072.459541374, 4581158.5714175375),
            new Cartesian3(-2357231.4925370603, -3745103.7886602185, 4580702.9757762635),
            new Cartesian3(-2355912.902205431, -3744249.029778454, 4582402.154378103),
            new Cartesian3(-2357208.0209552636, -3743553.4420488174, 4581961.863286629)],
        clampToGround : true,
        width : 2,
        material : Color.RED
    }
});
```

### How to draw a polygon?

```
viewer.entities.add({
    polygon: {
        hierarchy: positions,
        material: Cesium.Color.RED,
    }
});

```

## Scene

### How to disable draped imagery?

### How to make solid earth?

```

const viewer = new Cesium.Viewer("cesiumContainer", {
    imageryProvider: false,
    baseLayerPicker: false
});

```

### How to change the background of the scene?

```

viewer.scene.backgroundColor = Color.WHITE.clone();

```

### How to show quad tree tiles?

```

viewer.imageryLayers.addImageryProvider(
    new Cesium.TileCoordinatesImageryProvider()
);

```

### How to enable depth testing?

```

viewer.scene.globe.depthTestAgainstTerrain = true;

```

### How to show FPS?

```

viewer.scene.debugShowFramesPerSecond = true;

```

### How to hide the globe?

```

const viewer = new Cesium.Viewer('cesiumContainer', {
    globe: false
});

```

```

viewer.scene.globe.show = false;

```

### How to hide the sky and atmosphere?

```

viewer.scene.skyAtmosphere.show = false;
viewer.scene.skyBox.show = false;

```

```

const viewer = new Cesium.Viewer("cesiumContainer", {
    skyAtmosphere: false,
});

```

### How to hide the sun and moon?

```

viewer.scene.moon.show = false;
viewer.scene.sun.show = false;

```

### How to enable sun lighting?

```

viewer.scene.globe.enableLighting = true;

```

### How to enable shadow?

```

viewer.shadows = true;
viewer.scene.globe.enableLighting = true;
viewer.scene.globe.depthTestAgainstTerrain = true;

```

### How to limit rendering area

```

const coffeeBeltRectangle = Cesium.Rectangle.fromDegrees(-180.0, -23.43687, 180.0, 23.43687);
viewer.scene.globe.cartographicLimitRectangle = coffeeBeltRectangle;

```

### How to change maximumScreenSpaceError?

### How to force to load low level tiles?

```

viewer.scene.globe.maximumScreenSpaceError = 1000;

```

### How to get the height of scene geometry at the given cartographic position?

```

const height = scene.sampleHeight(carto);

```

## Terrain

### How to change terrain?

```

const terrainProvider = new Cesium.CesiumTerrainProvider({
    url: "your url",
});
viewer.scene.globe.terrainProvider = terrainProvider;

```

### How to get terrain height value?

```

const h = viewer.scene.globe.getHeight(Cesium.Cartographic.fromDegrees(longitude, latitude));

```

### How to render terrain in a wireframe?

```

viewer.scene.globe._surface.tileProvider._debug.wireframe = true;

```

### How to show back faces of terrain?

```

viewer.scene.globe.backFaceCulling = false;

```

### How can we determine the moment when all tiles of globe are loaded?

```

viewer.scene.globe.tileLoadProgressEvent.addEventListener(function (queuedTileCount) {
console.log(queuedTileCount);
console.log(viewer.scene.globe.tilesLoaded);

    if (viewer.scene.globe.tilesLoaded) {
        // add custom
    }

});

```

### Where are the skirt height of the tile determined?

function createQuantizedMeshTerrainData in client\src\cesium\CesiumTerrainProvider.js.

```

const skirtHeight = provider.getLevelMaximumGeometricError(level) * 5.0;

```

### How to get current rendering quadtree tiles?

```

viewer.scene.globe._surface._tilesToRender;

```

### Where are quadtree tiles for rendering selected?

function selectTilesForRendering in packages\engine\Source\Scene\QuadtreePrimitive.js.
actually addTileToRenderList.

### Where are terrain fill mesh updated?

in GlobeSurfaceTileProvider.js

```

TerrainFillMesh.updateFillTiles(this, this._quadtree._tilesToRender, frameState, this._vertexArraysToDestroy);

```

## 3D Tiles

### How to create a model matrix with position, heading-pitch-roll (hpr) and scale?

```

const position = new Cesium.Cartesian3(position.x, position.y, position.z);
const hpr = new Cesium.HeadingPitchRoll(
    headingPitchRoll.heading,
    headingPitchRoll.pitch,
    headingPitchRoll.roll
);
const scaleCartesian3 = new Cesium.Cartesian3(scale.x, scale.y, scale.z);
const modelMatrix = Cesium.Transforms.headingPitchRollToFixedFrame(position, hpr);
tileset.modelMatrix = Cesium.Matrix4.setScale(modelMatrix, scaleCartesian3, new Cesium.Matrix4());

```

### How can we determine the moment when all tiles of 3D Tiles are loaded?

```
tilesets.allTilesLoaded.addEventListener(function() {
    console.log('All tiles are loaded');
});
```

## Shader

### How to add or override builtin shader?

```

Cesium.ShaderSource._czmBuiltinsAndUniforms["test"] = "test"

```

## MISC

### how to remove(hide, disable) selection indicator when I click an entity?

```
const viewer = new Cesium.Viewer('cesiumContainer', {
    selectionIndicator: false
});

```

```
viewer.selectedEntityChanged.addEventListener(function (newEntity) {
    viewer.selectedEntity = undefined;
    // to do your custom logic
});

```

```
viewer._enableInfoOrSelection = false;

```

### How to get screenshot for viewer?

```

viewer.scene.requestRender();
viewer.render();

const quality = 0.5;
const dataUrl = viewer.canvas.toDataURL("image/jpg", quality);

const downloadURL = function(data, fileName) {
    const a = document.createElement('a');
    a.href = data;
    a.download = fileName;
    document.body.appendChild(a);
    a.style = 'display: none';
    a.click();
    a.remove();
};

downloadURL(dataUrl, 'capture.jpg');

setTimeout(function() {
    return window.URL.revokeObjectURL(dataUrl);
}, 1000);

```

### How to override the home button click?

```
viewer.homeButton.viewModel.command.beforeExecute.addEventListener(function (e) {
    e.cancel = true;
    // Add any custom code here, such as flying to some saved view.
});

```

### How to show error message?

```
viewer.cesiumWidget.showErrorPanel("error title", "This is a custom error message", "error");

```
