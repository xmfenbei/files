# Spatial Geohash Functions

Geohash is a system for encoding latitude and longitude coordinates of arbitrary precision into a text string. Geohash values are strings that contain only characters chosen from "0123456789bcdefghjkmnpqrstuvwxyz".

## ST_GeoHash(longitude, latitude, max_length), ST_GeoHash(point, max_length)
For the first syntax, the `longitude` must be a number **in the range [−180, 180]**, and the `latitude` must be a number **in the range [−90, 90]**. For the second syntax, a `POINT` value is required, where the X and Y coordinates are **in the valid ranges for longitude and latitude**, respectively.

The resulting string is no longer than `max_length` characters, which has an upper limit of **100**.

```
mysql> SELECT ST_GeoHash(180,0,10), ST_GeoHash(-180,-90,15);
+----------------------+-------------------------+
| ST_GeoHash(180,0,10) | ST_GeoHash(-180,-90,15) |
+----------------------+-------------------------+
| xbpbpbpbpb           | 000000000000000         |
+----------------------+-------------------------+
```

## ST_LatFromGeoHash(geohash_str)
The ST_LatFromGeoHash() decoding function reads no more than 433 characters from the geohash_str argument.

```
mysql> SELECT ST_LatFromGeoHash(ST_GeoHash(45,-20,10));
+------------------------------------------+
| ST_LatFromGeoHash(ST_GeoHash(45,-20,10)) |
+------------------------------------------+
|                                      -20 |
+------------------------------------------+
```

## ST_LongFromGeoHash(geohash_str)

```
mysql> SELECT ST_LongFromGeoHash(ST_GeoHash(45,-20,10));
+-------------------------------------------+
| ST_LongFromGeoHash(ST_GeoHash(45,-20,10)) |
+-------------------------------------------+
|                                        45 |
+-------------------------------------------+
```

## ST_PointFromGeoHash(geohash_str, srid)
The srid argument is an unsigned 32-bit integer.
```
mysql> SET @gh = ST_GeoHash(45,-20,10);
mysql> SELECT ST_AsText(ST_PointFromGeoHash(@gh,0));
+---------------------------------------+
| ST_AsText(ST_PointFromGeoHash(@gh,0)) |
+---------------------------------------+
| POINT(45 -20)                         |
+---------------------------------------+
```
问题：srid的用途
srid，指的是空间引用标识符, 默认为0.


#### 参考资料：
[12.15.10 Spatial Geohash Functions](https://dev.mysql.com/doc/refman/5.7/en/spatial-geohash-functions.html)

[SRID (空间引用识别号, 坐标系)](https://yq.aliyun.com/articles/137044)
