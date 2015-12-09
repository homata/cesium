This page describes the possible content of a CZML document or stream.  Please read [[CZML Structure]] for an explanation of how a CZML document is put together.

_NOTE: This is a work in progress and reflects our plans NOT our current capabilities._

## id

The ID of the object described by this packet.  IDs do not need to be GUIDs, but they do need to uniquely identify a single object within a CZML source and any other CZML sources loaded into the same scope.  If this property is not specified, the client will automatically generate a unique one.  However, this prevents later packets from referring to this object in order to, for example, add more data to it.

**Property Name**: `id`

**Interpolatable**: no

## name

The name of the object.  It does not have to be unique and is intended for user consumption.

**Property Name**: `name`

**Interpolatable**: no

## parent

The ID of the parent object or folder.

**Property Name**: `parent`

**Interpolatable**: no

## description

An HTML description of the object.

**Property Name**: `description`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `string` | Interval | string | The string value. |
| `reference` | Interval | string | A reference property. |

## availability

When data for an object is available. If data for an object is known to be available at the current animation time, but the client does not yet have that data (presumably because it will arrive in a later packet), the client will pause with a message like "Buffering..." while it waits to receive the data. The property can be a single string specifying a single interval, or an array of strings representing intervals.  A later Cesium packet can update this availability if it changes or is found to be incorrect. For example, an SGP4 propagator may report availability for all time, but then later the propagator throws an exception and the availability needs to be adjusted. If this optional property is not present, the object is assumed to be available for all time. Availability is scoped to a particular CZML stream, so two different streams can list different availability for a single object. Within a single stream, the last availability stated for an object is the one in effect and any availabilities in previous packets are ignored. If an object is available at a time, the client expects the object to have at least one property, and it expects all properties that it needs to be defined at that time. If the object doesn't have any properties, or a needed property is defined but not at the animation time, the client will pause animation and wait for more data.

**Property Name**: `availability`

**Interpolatable**: no

## position

The position of the object in the world. The position has no direct visual representation, but it is used to locate billboards, labels, and other primitives attached to the object.

**Property Name**: `position`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `referenceFrame` | Interval | string | The reference frame in which cartesian positions are specified. Possible values are "FIXED" and "INERTIAL". In addition, the value of this property can be a hash (#) symbol followed by the ID of another object in the same scope whose "position" and "orientation" properties define the reference frame in which this position is defined.  This property is ignored when specifying position with any type other than cartesian. If this property is not specified, the default reference frame is "FIXED". |
| `cartesian` | Interval | array | The position represented as a Cartesian `[X, Y, Z]` in the meters relative to the `referenceFrame`. If the array has three elements, the position is constant. If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `cartographicRadians` | Interval | array | The position represented as a WGS 84 Cartographic `[Longitude, Latitude, Height]` where longitude and latitude are in radians and height is in meters. If the array has three elements, the position is constant. If it has four or more elements, they are time-tagged samples arranged as `[Time, Longitude, Latitude, Height, Time, Longitude, Latitude, Height, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `cartographicDegrees` | Interval | array | The position reprsented as a WGS 84 Cartographic `[Longitude, Latitude, Height]` where longitude and latitude are in degrees and height is in meters. If the array has three elements, the position is constant. If it has four or more elements, they are time-tagged samples arranged as `[Time, Longitude, Latitude, Height, Time, Longitude, Latitude, Height, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `cartesianVelocity` | Interval | array | The position and velocity represented as two Cartesians `[X, Y, Z, vX, vY, vZ]` in the meters relative to the `referenceFrame`. If the array has six elements, the position is constant. If it has seven or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, vX, vY, vZ, Time, X, Y, Z, vX, vY, vZ, Time, X, Y, Z, vX, vY, vZ, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

Examples:

```javascript
{
  "id": "MyObject",
  "position": { "cartographicDegrees": [-75.0, 40.0, 0.0] }
}
```

```javascript
{
  "id": "InternationalSpaceStation",
  "position": {
    "referenceFrame": "INERTIAL",
    "epoch": "2012-05-02T12:00:00Z",
    "cartesian": [
      0.0, -6668447.2211117, 1201886.45913705, 146789.427467256,
      60.0, -6711432.84684144, 919677.673492462, -214047.552431458,
      90.0, -6721319.92231553, 776899.784034099, -394198.837519575,
      150.0, -6717826.447064, 488820.628328182, -752924.980158179,
      180.0, -6704450.41462847, 343851.784836767, -931084.800346031,
      240.0, -6654518.44949696, 52891.726433174, -1283967.69137678
    ],
    "nextTime": 300.0,
    "interpolationAlgorithm": "LAGRANGE",
    "interpolationDegree": 5
  }
}
```

## billboard

A billboard, or viewport-aligned image. The billboard is positioned in the scene by the position property. A billboard is sometimes called a marker.

**Property Name**: `billboard`

**Interpolatable**: no

### billboard.color

The color of the billboard.  This color value is multiplied with the values of the billboard's "image" to produce the final color.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.eyeOffset

The eye offset of the billboard, which is the offset in eye coordinates at which to place the billboard relative to the `position` property.  Eye coordinates are a left-handed coordinate system where the X-axis points toward the viewer's right, the Y-axis points up, and the Z-axis points into the screen.

**Property Name**: `eyeOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian` | Interval | array | The eye offset specified as a Cartesian `[X, Y, Z]` position in eye coordinates in  meters.  If the array has three elements, the eye offset is constant.  If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.horizontalOrigin

The horizontal origin of the billboard.  It controls whether the billboard image is left-, center-, or right-aligned with the `position`.

**Property Name**: `horizontalOrigin`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `horizontalOrigin` | Interval | string | The horizontal origin.  Valid values are "LEFT", "CENTER", and "RIGHT". |
| `reference` | Interval | string | A reference property. |

### billboard.image

The image displayed on the billboard, expressed as a URL.  For broadest client compatibility, the URL should be accessible via Cross-Origin Resource Sharing (CORS).  The URL may also be a <a href="https://developer.mozilla.org/en/data_URIs">data URI</a>.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

### billboard.pixelOffset

The offset, in viewport pixels, of the billboard origin from the `position`.  A pixel offset is the number of pixels up and to the right to place the billboard, relative to the `position`.

**Property Name**: `pixelOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The pixel offset specified as a Cartesian `[X, Y]` in viewport coordinates in pixels, where X is pixels to the right and Y is pixels up.  If the array has two elements, the pixel offset is constant.  If it has three or more elements, they are time-tagged samples arranged as `[Time, X, Y, Time, X, Y, Time, X, Y, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.scale

The scale of the billboard.  The scale is multiplied with the pixel size of the billboard's `image`.  For example, if the scale is 2.0, the billboard will be rendered with twice the number of pixels, in each direction, of the `image`.

**Property Name**: `scale`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.rotation

The rotation of the billboard offset from the alignedAxes.

**Property Name**: `rotation`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.alignedAxis

The aligned axis is the unit vector, in world coordinates, that the billboard up vector points towards. The default is the zero vector, which means the billboard is aligned to the screen up vector.

**Property Name**: `alignedAxis`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian` | Interval | array | The axis specified as a unit Cartesian `[X, Y, Z]` in world coordinates in  meters.  If the array has three elements, the eye offset is constant.  If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### billboard.show

Whether or not the billboard is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### billboard.verticalOrigin

The vertical origin of the billboard.  It controls whether the billboard image is bottom-, center-, or top-aligned with the `position`.

**Property Name**: `verticalOrigin`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `verticalOrigin` | Interval | string | The vertical origin.  Valid values are "BOTTOM", "CENTER", and "TOP". |
| `reference` | Interval | string | A reference property. |


## orientation

The orientation of the object in the world.  The orientation has no direct visual representation, but it is used to orient models, cones, and pyramids attached to the object.

**Property Name**: `orientation`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `axes` | Interval | string | TODO |
| `unitQuaternion` | Interval | array | TODO |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

## point

A point, or viewport-aligned circle.  The point is positioned in the scene by the `position` property.

**Property Name**: `point`

**Interpolatable**: no

### point.color

The color of the point.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### point.outlineColor

The color of the outline of the point.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### point.outlineWidth

The width of the outline of the point.

**Property Name**: `outlineWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### point.pixelSize

The size of the point, in pixels.

**Property Name**: `pixelSize`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### point.show

Whether or not the point is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |


## label

A string of text.  The label is positioned in the scene by the `position` property.

**Property Name**: `label`

**Interpolatable**: no

### label.eyeOffset

The eye offset of the label, which is the offset in eye coordinates at which to place the label relative to the `position` property.  Eye coordinates are a left-handed coordinate system where the X-axis points toward the viewer's right, the Y-axis points up, and the Z-axis points into the screen.

**Property Name**: `eyeOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian` | Interval | array | The eye offset specified as a Cartesian `[X, Y, Z]` position in eye coordinates in  meters.  If the array has three elements, the eye offset is constant.  If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.fillColor

The fill color of the label.

**Property Name**: `fillColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.font

The font to use for the label.

**Property Name**: `font`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `font` | Interval | string | The font. |
| `reference` | Interval | string | A reference property. |

### label.horizontalOrigin

The horizontal origin of the label.  It controls whether the label is left-, center-, or right-aligned with the `position`.

**Property Name**: `horizontalOrigin`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `horizontalOrigin` | Interval | string | The horizontal origin.  Valid values are "LEFT", "CENTER", and "RIGHT". |
| `reference` | Interval | string | A reference property. |

### label.outlineColor

The outline color of the label.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.outlineWidth

The outline width of the label.

**Property Name**: `outlineWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.pixelOffset

The offset, in viewport pixels, of the label origin from the `position`.  A pixel offset is the number of pixels up and to the right to place the label, relative to the `position`.

**Property Name**: `pixelOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The pixel offset specified as a Cartesian `[X, Y]` in viewport coordinates in pixels, where X is pixels to the right and Y is pixels up.  If the array has two elements, the pixel offset is constant.  If it has three or more elements, they are time-tagged samples arranged as `[Time, X, Y, Time, X, Y, Time, X, Y, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.scale

The scale of the label.  The scale is multiplied with the pixel size of the label's text.  For example, if the scale is 2.0, the label will be rendered with twice the number of pixels, in each direction, of the text.

**Property Name**: `scale`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### label.show

Whether or not the label is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### label.style

The style of the label.

**Property Name**: `style`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `labelStyle` | Interval | string | The label style.  Valid values are "FILL", "OUTLINE", and "FILL_AND_OUTLINE". |
| `reference` | Interval | string | A reference property. |

### label.text

The text displayed by the label.

**Property Name**: `text`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `string` | Interval | string | The string value. |
| `reference` | Interval | string | A reference property. |

### label.verticalOrigin

The vertical origin of the label.  It controls whether the label image is bottom-, center-, or top-aligned with the `position`.

**Property Name**: `verticalOrigin`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `verticalOrigin` | Interval | string | The vertical origin.  Valid values are "BOTTOM", "CENTER", and "TOP". |
| `reference` | Interval | string | A reference property. |


## polyline

A polyline, which is a line in the scene composed of multiple segments.

**Property Name**: `polyline`

**Interpolatable**: no

### polyline.positions

The array of positions defining the polyline as a line strip.

**Property Name**: `positions`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `referenceFrame` | Interval | string | The reference frame in which cartesian positions are specified. Possible values are "FIXED" and "INERTIAL". In addition, the value of this property can be a hash (#) symbol followed by the ID of another object in the same scope whose "position" and "orientation" properties define the reference frame in which this position is defined.  This property is ignored when specifying position with any type other than cartesian. If this property is not specified, the default reference frame is "FIXED". |
| `cartesian` | Interval | array | The list of positions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]` in the meters relative to the `referenceFrame`. |
| `cartographicRadians` | Interval | array | The list of positions represented as WGS 84 `[Longitude, Latitude, Height, Longitude, Latitude, Height, ...]` where longitude and latitude are in radians and height is in meters. |
| `cartographicDegrees` | Interval | array | The list of positions represented as WGS 84 `[Longitude, Latitude, Height, Longitude, Latitude, Height, ...]` where longitude and latitude are in degrees and height is in meters. |
| `references` | Interval | array | The list of positions specified as references.  Each reference is to a property that defines a single position, possible as it changes with time. |

### polyline.show

Whether or not the polyline is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### polyline.material

The material to use to draw the polyline.

**Property Name**: `material`

**Interpolatable**: no

#### polyline.material.solidColor

Colors the line with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### polyline.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### polyline.material.polylineOutline

Colors the line with a color and outline.

**Property Name**: `polylineOutline`

**Interpolatable**: no

##### polyline.material.polylineOutline.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polyline.material.polylineOutline.outlineColor

The color of the surface outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polyline.material.polylineOutline.outlineWidth

The width of the outline.

**Property Name**: `outlineWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### polyline.material.polylineGlow

Colors the line with a glowing color.

**Property Name**: `polylineGlow`

**Interpolatable**: no

##### polyline.material.polylineGlow.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polyline.material.polylineGlow.glowPower

The strength of the glow.

**Property Name**: `glowPower`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### polyline.width

The width of the polyline.

**Property Name**: `width`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polyline.followSurface

Whether or not the positions are connected as great arcs (the default) or as straight lines.

**Property Name**: `followSurface`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |


## path

A path, which is a polyline defined by the motion of an object over time.  The possible vertices of the path are specified by the `position` property.

**Property Name**: `path`

**Interpolatable**: no

### path.show

Whether or not the path is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### path.material

The material to use to draw the path.

**Property Name**: `material`

**Interpolatable**: no

#### path.material.solidColor

Colors the line with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### path.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### path.material.polylineOutline

Colors the line with a color and outline.

**Property Name**: `polylineOutline`

**Interpolatable**: no

##### path.material.polylineOutline.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### path.material.polylineOutline.outlineColor

The color of the surface outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### path.material.polylineOutline.outlineWidth

The width of the outline.

**Property Name**: `outlineWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### path.material.polylineGlow

Colors the line with a glowing color.

**Property Name**: `polylineGlow`

**Interpolatable**: no

##### path.material.polylineGlow.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### path.material.polylineGlow.glowPower

The strength of the glow.

**Property Name**: `glowPower`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### path.width

The width of the path line.

**Property Name**: `width`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### path.resolution

The maximum step-size, in seconds, used to sample the path.  If the `position` property has data points farther apart than resolution specfies, additional steps will be taken, creating a smoother path.

**Property Name**: `resolution`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### path.leadTime

The time ahead of the animation time, in seconds, to show the path.

**Property Name**: `leadTime`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### path.trailTime

The time behind the animation time, in seconds, to show the path.

**Property Name**: `trailTime`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


## polygon

A polygon, which is a closed figure on the surface of the Earth.

**Property Name**: `polygon`

**Interpolatable**: no

### polygon.positions

The array of positions defining a simple polygon.

**Property Name**: `positions`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `referenceFrame` | Interval | string | The reference frame in which cartesian positions are specified. Possible values are "FIXED" and "INERTIAL". In addition, the value of this property can be a hash (#) symbol followed by the ID of another object in the same scope whose "position" and "orientation" properties define the reference frame in which this position is defined.  This property is ignored when specifying position with any type other than cartesian. If this property is not specified, the default reference frame is "FIXED". |
| `cartesian` | Interval | array | The list of positions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]` in the meters relative to the `referenceFrame`. |
| `cartographicRadians` | Interval | array | The list of positions represented as WGS 84 `[Longitude, Latitude, Height, Longitude, Latitude, Height, ...]` where longitude and latitude are in radians and height is in meters. |
| `cartographicDegrees` | Interval | array | The list of positions represented as WGS 84 `[Longitude, Latitude, Height, Longitude, Latitude, Height, ...]` where longitude and latitude are in degrees and height is in meters. |
| `references` | Interval | array | The list of positions specified as references.  Each reference is to a property that defines a single position, possible as it changes with time. |

### polygon.show

Whether or not the polygon is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### polygon.material

The material to use to fill the polygon.

**Property Name**: `material`

**Interpolatable**: no

#### polygon.material.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### polygon.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### polygon.material.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### polygon.material.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### polygon.material.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### polygon.material.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### polygon.material.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### polygon.material.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### polygon.material.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### polygon.material.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### polygon.material.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### polygon.height

The height of the polygon when perPositionHeight is false.

**Property Name**: `height`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polygon.extrudedHeight

The extruded height of the polygon.

**Property Name**: `extrudedHeight`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polygon.granularity

The sampling distance, in radians.

**Property Name**: `granularity`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polygon.stRotation

The rotation of any applied texture.

**Property Name**: `stRotation`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polygon.fill

Whether or not the polygon is filled.

**Property Name**: `fill`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### polygon.outline

Whether or not the polygon is outlined.

**Property Name**: `outline`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### polygon.outlineColor

The color of the polygon outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### polygon.perPositionHeight

Whether to use the height of each position to define the polygon or a constant height above the surface.

**Property Name**: `perPositionHeight`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |


## ellipsoid

An ellipsoid, which is a closed quadric surface that is a three dimensional analogue of an ellipse.  The ellipsoid is positioned and oriented using the `position` and `orientation` properties.

**Property Name**: `ellipsoid`

**Interpolatable**: no

### ellipsoid.show

Whether or not the ellipsoid is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipsoid.radii

The dimensions of the ellipsoid.

**Property Name**: `radii`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian` | Interval | array | The radii as a Cartesian `[X, Y, Z]` in meters. If the array has three elements, the radii are constant.  If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where _Time_ is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipsoid.fill

Whether or not the ellipsoid is filled.

**Property Name**: `fill`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipsoid.material

The material to display on the surface of the ellipsoid.

**Property Name**: `material`

**Interpolatable**: no

#### ellipsoid.material.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### ellipsoid.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipsoid.material.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### ellipsoid.material.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### ellipsoid.material.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipsoid.material.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### ellipsoid.material.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipsoid.material.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### ellipsoid.material.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### ellipsoid.material.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipsoid.material.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### ellipsoid.outline

Whether or not the ellipsoid is outlined.

**Property Name**: `outline`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipsoid.outlineColor

The color of the ellipsoid outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipsoid.stackPartitions

The number of times to partition the ellipsoid into stacks.

**Property Name**: `stackPartitions`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipsoid.slicePartitions

The number of times to partition the ellipsoid into radial slices.

**Property Name**: `slicePartitions`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipsoid.subdivisions

The number of points per outline line, determining the granularity of the curvature.

**Property Name**: `subdivisions`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


## viewFrom

A suggested camera location when viewing this object.  The property is specified as a Cartesian position in the East (x), North (y), Up (z) reference frame relative to the objects position property.

**Property Name**: `viewFrom`

**Interpolatable**: no

## model

A 3D model.  The model is positioned and oriented using the `position` and `orientation` properties.

**Property Name**: `model`

**Interpolatable**: no

### model.show

Whether or not the model is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### model.scale

The scale of the model.

**Property Name**: `scale`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### model.minimumPixelSize

The approximate minimum pixel size of the model regardless of zoom.

**Property Name**: `minimumPixelSize`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### model.gltf

The URL of a <a href="https://github.com/KhronosGroup/glTF">glTF</a> model.

**Property Name**: `gltf`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |


## ellipse

An ellipse, which is a closed curve on the surface of the Earth.  The ellipse is positioned using the `position` property.

**Property Name**: `ellipse`

**Interpolatable**: no

### ellipse.show

Whether or not the ellipse is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipse.semiMajorAxis

The length of the ellipse's semi-major axis in meters.

**Property Name**: `semiMajorAxis`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.semiMinorAxis

The length of the ellipse's semi-minor axis in meters.

**Property Name**: `semiMinorAxis`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.rotation

The angle from north (counter-clockwise) in radians.

**Property Name**: `rotation`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.material

The material to use to fill the ellipse.

**Property Name**: `material`

**Interpolatable**: no

#### ellipse.material.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### ellipse.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipse.material.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### ellipse.material.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### ellipse.material.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipse.material.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### ellipse.material.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### ellipse.material.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### ellipse.material.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### ellipse.material.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### ellipse.material.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### ellipse.height

The height of the ellipse when perPositionHeight is false.

**Property Name**: `height`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.extrudedHeight

The extruded height of the ellipse.

**Property Name**: `extrudedHeight`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.granularity

The sampling distance, in radians.

**Property Name**: `granularity`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.stRotation

The rotation of any applied texture coordinates.

**Property Name**: `stRotation`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.fill

Whether or not the ellipse is filled.

**Property Name**: `fill`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipse.outline

Whether or not the ellipse is outlined.

**Property Name**: `outline`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### ellipse.outlineColor

The color of the ellipse outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### ellipse.numberOfVerticalLines

The number of vertical lines to use when outlining an extruded ellipse.

**Property Name**: `numberOfVerticalLines`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


## clock

The clock settings for the entire data set. Only valid on the document object.

**Property Name**: `clock`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `currentTime` | Interval | string | The current time. |
| `multiplier` | Interval | number | The multiplier, which in TICK_DEPENDENT mode is the number of seconds to advance each tick.  In SYSTEM_CLOCK_DEPENDENT mode, it is the multiplier applied to the amount of time elapsed between ticks.  This value is ignored in SYSTEM_CLOCK mode. |
| `range` | Interval | string | The behavior of a clock when its current time reaches its start or end points.  Valid values are 'UNBOUNDED', 'CLAMPED', and 'LOOP_STOP'. |
| `step` | Interval | string | Defines how a clock steps in time.  Valid values are 'SYSTEM_CLOCK', 'SYSTEM_CLOCK_MULTIPLIER', and 'TICK_DEPENDENT'. |

## version

The CZML version being written. Only valid on the document object.

**Property Name**: `version`

**Interpolatable**: no

## agi_conicSensor

A conical sensor volume taking into account occlusion of an ellipsoid, i.e., the globe.

_Note: This type is an extension and may not be implemented by all CZML clients._

**Property Name**: `agi_conicSensor`

**Interpolatable**: no

### agi_conicSensor.show

Whether or not the cone is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.innerHalfAngle

The inner half angle of the cone.

**Property Name**: `innerHalfAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.outerHalfAngle

The outer half angle of the cone.

**Property Name**: `outerHalfAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.minimumClockAngle

The minimum clock angle limit of the cone.

**Property Name**: `minimumClockAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.maximumClockAngle

The maximum clock angle limit of the cone.

**Property Name**: `maximumClockAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.radius

The radial limit of the cone.

**Property Name**: `radius`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.showIntersection

Whether or not the intersection of the cone with the Earth is shown.

**Property Name**: `showIntersection`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.intersectionColor

The color of the intersection of the cone with the Earth.

**Property Name**: `intersectionColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.intersectionWidth

The width of the intersection in pixels.

**Property Name**: `intersectionWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_conicSensor.showLateralSurfaces

Whether or not the intersections of the cone with the earth are shown.

**Property Name**: `showLateralSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.lateralSurfaceMaterial

Whether or not lateral surfaces are shown.

**Property Name**: `lateralSurfaceMaterial`

**Interpolatable**: no

#### agi_conicSensor.lateralSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_conicSensor.lateralSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.lateralSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_conicSensor.lateralSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.lateralSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.lateralSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_conicSensor.lateralSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.lateralSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_conicSensor.lateralSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.lateralSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.lateralSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_conicSensor.showEllipsoidSurfaces

Whether or not ellipsoid surfaces are shown.

**Property Name**: `showEllipsoidSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.ellipsoidSurfaceMaterial

The material to use for the cone's ellipsoid surface.

**Property Name**: `ellipsoidSurfaceMaterial`

**Interpolatable**: no

#### agi_conicSensor.ellipsoidSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_conicSensor.showEllipsoidHorizonSurfaces

Whether or not ellipsoid horizon surfaces are shown.

**Property Name**: `showEllipsoidHorizonSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.ellipsoidHorizonSurfaceMaterial

The material to use for the cone's ellipsoid horizon surface.

**Property Name**: `ellipsoidHorizonSurfaceMaterial`

**Interpolatable**: no

#### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.ellipsoidHorizonSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_conicSensor.showDomeSurfaces

Whether or not dome surfaces are shown.

**Property Name**: `showDomeSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_conicSensor.domeSurfaceMaterial

The material to use for the cone's dome.

**Property Name**: `domeSurfaceMaterial`

**Interpolatable**: no

#### agi_conicSensor.domeSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_conicSensor.domeSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.domeSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_conicSensor.domeSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.domeSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.domeSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_conicSensor.domeSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_conicSensor.domeSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_conicSensor.domeSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_conicSensor.domeSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_conicSensor.domeSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_conicSensor.portionToDisplay

Indicates what part of a sensor should be displayed.

**Property Name**: `portionToDisplay`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `portionToDisplay` | Interval | string | Indicates what part of a sensor should be displayed.  Valid values are "COMPLETE", "BELOW_ELLIPSOID_HORIZON", "ABOVE_ELLIPSOID_HORIZON". |
| `reference` | Interval | string | A reference property. |


## agi_customPatternSensor

A custom sensor volume taking into account occlusion of an ellipsoid, i.e., the globe.

_Note: This type is an extension and may not be implemented by all CZML clients._

**Property Name**: `agi_customPatternSensor`

**Interpolatable**: no

### agi_customPatternSensor.show

Whether or not the pyramid is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.directions

The list of directions defining the pyramid.

**Property Name**: `directions`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `spherical` | Interval | array | The list of directions represented as a clock angle, a cone angle, both in radians, and magnitude in meters.  The clock angle is measured in the XY plane from the positive X axis toward the positive Y axis.  The cone angle is the angle from the positive Z axis toward the negative Z axis. |
| `unitSpherical` | Interval | array | The list of directions represented as a clock angle and a cone angle, both in radians.  The clock angle is measured in the XY plane from the positive X axis toward the positive Y axis.  The cone angle is the angle from the positive Z axis toward the negative Z axis. |
| `cartesian` | Interval | array | The list of directions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]` |
| `unitCartesian` | Interval | array | The list of directions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]`. |

### agi_customPatternSensor.radius

The radial limit of the pyramid.

**Property Name**: `radius`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_customPatternSensor.showIntersection

Whether or not the intersection of the pyramid with the Earth is shown.

**Property Name**: `showIntersection`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.intersectionColor

The color of the intersection of the pyramid with the Earth.

**Property Name**: `intersectionColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_customPatternSensor.intersectionWidth

The width of the intersection in pixels.

**Property Name**: `intersectionWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_customPatternSensor.showLateralSurfaces

Whether or not the intersections of the pyramid with the earth are shown.

**Property Name**: `showLateralSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.lateralSurfaceMaterial

Whether or not lateral surfaces are shown.

**Property Name**: `lateralSurfaceMaterial`

**Interpolatable**: no

#### agi_customPatternSensor.lateralSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_customPatternSensor.lateralSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.lateralSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_customPatternSensor.lateralSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.lateralSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.lateralSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_customPatternSensor.lateralSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.lateralSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_customPatternSensor.lateralSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.lateralSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.lateralSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_customPatternSensor.showEllipsoidSurfaces

Whether or not ellipsoid surfaces are shown.

**Property Name**: `showEllipsoidSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.ellipsoidSurfaceMaterial

The material to use for the pyramid's ellipsoid surface.

**Property Name**: `ellipsoidSurfaceMaterial`

**Interpolatable**: no

#### agi_customPatternSensor.ellipsoidSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_customPatternSensor.showEllipsoidHorizonSurfaces

Whether or not ellipsoid horizon surfaces are shown.

**Property Name**: `showEllipsoidHorizonSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial

The material to use for the pyramid's ellipsoid horizon surface.

**Property Name**: `ellipsoidHorizonSurfaceMaterial`

**Interpolatable**: no

#### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.ellipsoidHorizonSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_customPatternSensor.showDomeSurfaces

Whether or not dome surfaces are shown.

**Property Name**: `showDomeSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_customPatternSensor.domeSurfaceMaterial

The material to use for the pyramid's dome.

**Property Name**: `domeSurfaceMaterial`

**Interpolatable**: no

#### agi_customPatternSensor.domeSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_customPatternSensor.domeSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.domeSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_customPatternSensor.domeSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.domeSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.domeSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_customPatternSensor.domeSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_customPatternSensor.domeSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_customPatternSensor.domeSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_customPatternSensor.domeSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_customPatternSensor.domeSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_customPatternSensor.portionToDisplay

Indicates what part of a sensor should be displayed.

**Property Name**: `portionToDisplay`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `portionToDisplay` | Interval | string | Indicates what part of a sensor should be displayed.  Valid values are "COMPLETE", "BELOW_ELLIPSOID_HORIZON", "ABOVE_ELLIPSOID_HORIZON". |
| `reference` | Interval | string | A reference property. |


## agi_fan

Defines a fan, which starts at a point or apex and extends in a specified list of directions from the apex.  Each pair of directions forms a face of the fan extending to the specified radius.

_Note: This type is an extension and may not be implemented by all CZML clients._

**Property Name**: `agi_fan`

**Interpolatable**: no

### agi_fan.show

Whether or not the fan is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_fan.directions

The list of directions defining the fan.

**Property Name**: `directions`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `spherical` | Interval | array | The list of directions represented as a clock angle, a cone angle, both in radians, and magnitude in meters.  The clock angle is measured in the XY plane from the positive X axis toward the positive Y axis.  The cone angle is the angle from the positive Z axis toward the negative Z axis. |
| `unitSpherical` | Interval | array | The list of directions represented as a clock angle and a cone angle, both in radians.  The clock angle is measured in the XY plane from the positive X axis toward the positive Y axis.  The cone angle is the angle from the positive Z axis toward the negative Z axis. |
| `cartesian` | Interval | array | The list of directions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]` |
| `unitCartesian` | Interval | array | The list of directions represented as Cartesian `[X, Y, Z, X, Y, Z, ...]`. |

### agi_fan.radius

The radial limit of the fan.

**Property Name**: `radius`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_fan.perDirectionRadius

When true, the magnitude of each direction is used instead of a constant radius.

**Property Name**: `perDirectionRadius`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_fan.material

The material to display on the surface of the fan.

**Property Name**: `material`

**Interpolatable**: no

#### agi_fan.material.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_fan.material.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_fan.material.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_fan.material.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_fan.material.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_fan.material.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_fan.material.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_fan.material.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_fan.material.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_fan.material.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_fan.material.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_fan.fill

Whether or not the fan is filled.

**Property Name**: `fill`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_fan.outline

Whether or not the fan is outlined.

**Property Name**: `outline`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_fan.numberOfRings

The number of outline rings to draw, starting from the outer edge and equidistantly spaced towards the center.

**Property Name**: `numberOfRings`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_fan.outlineColor

The color of the fan outline.

**Property Name**: `outlineColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


## agi_rectangularSensor

A rectangular pyramid sensor volume taking into account occlusion of an ellipsoid, i.e., the globe.

_Note: This type is an extension and may not be implemented by all CZML clients._

**Property Name**: `agi_rectangularSensor`

**Interpolatable**: no

### agi_rectangularSensor.show

Whether or not the pyramid is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.xHalfAngle

The X half angle.

**Property Name**: `xHalfAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_rectangularSensor.yHalfAngle

The Y half angle.

**Property Name**: `yHalfAngle`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_rectangularSensor.radius

The radial limit of the pyramid.

**Property Name**: `radius`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_rectangularSensor.showIntersection

Whether or not the intersection of the pyramid with the Earth is shown.

**Property Name**: `showIntersection`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.intersectionColor

The color of the intersection of the pyramid with the Earth.

**Property Name**: `intersectionColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_rectangularSensor.intersectionWidth

The width of the intersection in pixels.

**Property Name**: `intersectionWidth`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_rectangularSensor.showLateralSurfaces

Whether or not the intersections of the pyramid with the earth are shown.

**Property Name**: `showLateralSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.lateralSurfaceMaterial

Whether or not lateral surfaces are shown.

**Property Name**: `lateralSurfaceMaterial`

**Interpolatable**: no

#### agi_rectangularSensor.lateralSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_rectangularSensor.lateralSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.lateralSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_rectangularSensor.lateralSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.lateralSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.lateralSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_rectangularSensor.lateralSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.lateralSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_rectangularSensor.lateralSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.lateralSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.lateralSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_rectangularSensor.showEllipsoidSurfaces

Whether or not ellipsoid surfaces are shown.

**Property Name**: `showEllipsoidSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.ellipsoidSurfaceMaterial

The material to use for the pyramid's ellipsoid surface.

**Property Name**: `ellipsoidSurfaceMaterial`

**Interpolatable**: no

#### agi_rectangularSensor.ellipsoidSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_rectangularSensor.showEllipsoidHorizonSurfaces

Whether or not ellipsoid horizon surfaces are shown.

**Property Name**: `showEllipsoidHorizonSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial

The material to use for the pyramid's ellipsoid horizon surface.

**Property Name**: `ellipsoidHorizonSurfaceMaterial`

**Interpolatable**: no

#### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.ellipsoidHorizonSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_rectangularSensor.showDomeSurfaces

Whether or not dome surfaces are shown.

**Property Name**: `showDomeSurfaces`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_rectangularSensor.domeSurfaceMaterial

The material to use for the pyramid's dome.

**Property Name**: `domeSurfaceMaterial`

**Interpolatable**: no

#### agi_rectangularSensor.domeSurfaceMaterial.solidColor

Fills the surface with a solid color, which may be translucent.

**Property Name**: `solidColor`

**Interpolatable**: no

##### agi_rectangularSensor.domeSurfaceMaterial.solidColor.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.domeSurfaceMaterial.image

Fills the surface with an image.

**Property Name**: `image`

**Interpolatable**: no

##### agi_rectangularSensor.domeSurfaceMaterial.image.image

The image to display on the surface.

**Property Name**: `image`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `uri` | Interval | string | The URI value. |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.domeSurfaceMaterial.image.repeat

The number of times the image repeats along each axis.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The numger of times the image repeats along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.domeSurfaceMaterial.grid

Fills the surface with a grid.

**Property Name**: `grid`

**Interpolatable**: no

##### agi_rectangularSensor.domeSurfaceMaterial.grid.color

The color of the surface.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.grid.cellAlpha

Alpha value for the space between grid lines.  This will be combined with the color alpha.

**Property Name**: `cellAlpha`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.grid.lineCount

The number of grid lines along each axis.

**Property Name**: `lineCount`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The number of grid lines along each axis. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.grid.lineThickness

The thickness of grid lines along each axis, in pixels.

**Property Name**: `lineThickness`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The thickness of grid lines along each axis, in pixels. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.grid.lineOffset

The offset of grid lines along each axis, as a percentage from 0 to 1.

**Property Name**: `lineOffset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `cartesian2` | Interval | array | The offset of grid lines along each axis, as a percentage from 0 to 1. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


#### agi_rectangularSensor.domeSurfaceMaterial.stripe

Fills the surface with alternating colors.

**Property Name**: `stripe`

**Interpolatable**: no

##### agi_rectangularSensor.domeSurfaceMaterial.stripe.orientation

The value indicating if the stripes are horizontal or vertical.

**Property Name**: `orientation`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `StripeOrientation` | Interval | string | The orientation of stripes in the stripe material. Valid values are "HORIZONTAL" or "VERTICAL". |
| `reference` | Interval | string | A reference property. |

##### agi_rectangularSensor.domeSurfaceMaterial.stripe.evenColor

The even color.

**Property Name**: `evenColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.stripe.oddColor

The odd color.

**Property Name**: `oddColor`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.stripe.offset

The value indicating where in the pattern to begin drawing; with 0.0 being the beginning of the even color, 1.0 the beginning of the odd color, 2.0 being the even color again, and any multiple or fractional values being in between.

**Property Name**: `offset`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

##### agi_rectangularSensor.domeSurfaceMaterial.stripe.repeat

The number of time the stripes repeat.

**Property Name**: `repeat`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |



### agi_rectangularSensor.portionToDisplay

Indicates what part of a sensor should be displayed.

**Property Name**: `portionToDisplay`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `portionToDisplay` | Interval | string | Indicates what part of a sensor should be displayed.  Valid values are "COMPLETE", "BELOW_ELLIPSOID_HORIZON", "ABOVE_ELLIPSOID_HORIZON". |
| `reference` | Interval | string | A reference property. |


## agi_vector

Defines a graphical vector that originates at the `position` property and extends in the provided direction for the provided length.

_Note: This type is an extension and may not be implemented by all CZML clients._

**Property Name**: `agi_vector`

**Interpolatable**: no

### agi_vector.show

Whether or not the vector is shown.

**Property Name**: `show`

**Interpolatable**: no

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `boolean` | Interval | boolean | The boolean value. |

### agi_vector.color

The color of the vector.

**Property Name**: `color`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `rgba` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0-255. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `rgbaf` | Interval | array | The color specified as an array of color components [Red, Green, Blue, Alpha] where each component is in the range 0.0-1.0. If the array has four elements, the color is constant. If it has five or more elements, they are time-tagged samples arranged as [Time, Red, Green, Blue, Alpha, Time, Red, Green, Blue, Alpha, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_vector.direction

The direction of the vector.

**Property Name**: `direction`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `axes` | Interval | string | TODO |
| `spherical` | Interval | array | A direction specified as a spherical [Clock, Cone, Magnitude] angles in radians, distance in meters. If the array has three elements, the direction is constant. If it has four or more elements, they are time-tagged samples arranged as [Time, Clock, Cone, Magnitude, Time, Clock, Cone, Magnitude, Time, Clock, Cone, Magnitude, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `unitSpherical` | Interval | array | A direction specified as a unit spherical [Clock, Cone] angles in radians. If the array has two elements, the direction is constant. If it has three or more elements, they are time-tagged samples arranged as [Time, Clock, Cone, Time, Clock, Cone, Time, Clock, Cone, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `cartesian` | Interval | array | The direction represented as a unit Cartesian `[X, Y, Z]`. If the array has three elements, the position is constant. If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `unitCartesian` | Interval | array | The direction represented as a unit Cartesian `[X, Y, Z]`. If the array has three elements, the position is constant. If it has four or more elements, they are time-tagged samples arranged as `[Time, X, Y, Z, Time, X, Y, Z, Time, X, Y, Z, ...]`, where Time is an ISO 8601 date and time string or seconds since `epoch`. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_vector.length

The graphical length of the vector.

**Property Name**: `length`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |

### agi_vector.minimumLengthInPixels

The minimum graphical length of the vector in pixels.

**Property Name**: `minimumLengthInPixels`

**Interpolatable**: yes

**Sub-properties**:

| Name | Scope | Type | Description |
|:-----|:------|:-----|:------------|
| `number` | Interval | number or array | The floating-point value. The value may be a single number, in which case the value is constant over the interval, or it may be an array.  If it is an array and the array has one element, the value is constant over the interval. If it has two or more elements, they are time-tagged samples arranged as [Time, Value, Time, Value, ...], where Time is an ISO 8601 date and time string or seconds since epoch. |
| `reference` | Interval | string | A reference property. |
| `epoch` | Packet | string | Specifies the epoch to use for times specifies as seconds since an epoch. |
| `nextTime` | Packet | string or number | The time of the next sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |
| `previousTime` | Packet | string or number | The time of the previous sample within this interval, specified as either an ISO 8601 date and time string or as seconds since epoch. This property is used to determine if there is a gap between samples specified in different packets. |


