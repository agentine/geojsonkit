# geospec — GeoJSON for Python (RFC 7946)

**Replaces:** [jazzband/geojson](https://github.com/jazzband/geojson) (4.5M/month PyPI, 984 stars)
**Package name:** `geospec` (verified available on PyPI)
**Language:** Python (3.10+)
**License:** BSD-3-Clause

## Why

The jazzband/geojson package is at risk:
- Jazzband is sunsetting by end of 2026 (AI spam crisis, governance failure)
- Last meaningful commit: Dec 2024 (release 3.2.0)
- Python 3.14 compatibility PR sitting unmerged for 3 months
- 28 open issues, 25 open PRs — no maintainer response
- No standalone drop-in alternative exists (geojson-pydantic requires Pydantic, different API)

## Scope

Lightweight, zero-dependency Python library implementing RFC 7946 GeoJSON objects with:
- All 7 geometry types + Feature + FeatureCollection
- JSON serialization/deserialization with GeoJSON object hooks
- Coordinate validation per RFC 7946
- `__geo_interface__` protocol support
- Coordinate utility functions (extraction, transformation)

## Architecture

```
geospec/
├── __init__.py          # Public API re-exports
├── _types.py            # Type aliases (Coord2D, Coord3D, BBox, etc.)
├── base.py              # GeoJSON base class (dict subclass)
├── geometry.py          # Point, LineString, Polygon, Multi*, GeometryCollection
├── feature.py           # Feature, FeatureCollection
├── codec.py             # dump, dumps, load, loads, GeoJSONEncoder
├── validation.py        # RFC 7946 validation logic (separated from geometry)
├── utils.py             # coords, map_coords, map_tuples, map_geometries
└── py.typed             # PEP 561 marker
```

## Key Design Decisions

1. **Dict subclass** — same as jazzband/geojson. GeoJSON objects behave as dicts with attribute access. This ensures JSON serialization compatibility and familiar API.

2. **Separated validation** — validation logic in its own module for testability. Each geometry type delegates to validation functions.

3. **Type hints throughout** — full type annotations with `py.typed` marker. TypeAlias for coordinate types (`Coord2D = tuple[float, float]`, `Coord3D = tuple[float, float, float]`).

4. **RFC 7946 strictness** — optional strict mode validates:
   - Longitude range [-180, 180], Latitude range [-90, 90]
   - Right-hand rule for polygon winding order
   - Linear ring closure (first == last coordinate)
   - Minimum coordinate counts per geometry type

5. **Zero dependencies** — stdlib `json` only. No simplejson fallback (unnecessary for modern Python).

6. **API compatibility** — drop-in replacement for jazzband/geojson:
   - Same class names: `Point`, `LineString`, `Polygon`, `MultiPoint`, `MultiLineString`, `MultiPolygon`, `GeometryCollection`, `Feature`, `FeatureCollection`
   - Same functions: `dump`, `dumps`, `load`, `loads`, `coords`, `map_coords`
   - Same `__geo_interface__` protocol
   - Same `errors()` / `is_valid` API
   - Same dict-like behavior with attribute access

## Public API

### Classes
- `GeoJSON` — base class
- `Point(coordinates, validate=False, precision=6)`
- `LineString(coordinates, validate=False, precision=6)`
- `Polygon(coordinates, validate=False, precision=6)`
- `MultiPoint(coordinates, validate=False, precision=6)`
- `MultiLineString(coordinates, validate=False, precision=6)`
- `MultiPolygon(coordinates, validate=False, precision=6)`
- `GeometryCollection(geometries=None)`
- `Feature(id=None, geometry=None, properties=None)`
- `FeatureCollection(features)`

### Functions
- `dump(obj, fp)` / `dumps(obj)` — serialize to GeoJSON
- `load(fp)` / `loads(s)` — deserialize from GeoJSON
- `coords(obj)` — yield coordinate tuples from any GeoJSON object
- `map_coords(func, obj)` — apply function to each coordinate dimension
- `map_tuples(func, obj)` — apply function to each coordinate tuple
- `map_geometries(func, obj)` — apply function to each geometry

### Properties
- `obj.is_valid` — bool, True if no validation errors
- `obj.errors()` — list of validation error strings
- `obj.__geo_interface__` — GeoJSON-compatible dict

## Improvements Over jazzband/geojson

1. **Type hints** — full annotations, `py.typed` marker for IDE support
2. **RFC 7946 range validation** — optional lat/lon bounds checking
3. **Winding order validation** — check right-hand rule for polygons
4. **No simplejson dependency** — stdlib json only
5. **Python 3.10+** — modern syntax, no legacy compatibility code
6. **Separated validation module** — cleaner, more testable

## Deliverables

- [ ] Core implementation (all classes, codec, validation, utils)
- [ ] Comprehensive test suite (pytest)
- [ ] pyproject.toml with modern packaging
- [ ] CI/CD (GitHub Actions)
- [ ] Migration guide from jazzband/geojson
