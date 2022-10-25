# ARES File Format

Augmented Reality Education Storage

## Features

* Vertex Color
* Animations with per frame vertex position and color data
* Additional data per vertex (and frame)

## Structure

The first byte / uint8 value represents the format identifier

### Format identifier

| id  | Structure                                                             |
|-----|-----------------------------------------------------------------------|
| 0   | [Default structure](#default-structure)                               |
| 1   | [Single frame animation structure](#single-frame-animation-structure) |
| 2   | [Rigid body animation structure](#rigid-body-animation-structure)     |

### Default structure

<details>
  <summary>Detailed structure information</summary>

Consists out of:

* 1 Header
* 1 Base mesh
* n Data blocks according to the header

#### Header structure

| Length in bytes | Data Type | Description                 |
|-----------------|-----------|-----------------------------|
| 4               | uint32    | Amount of vertices          |
| 1               | uint8     | Amount of vertices per face |
| 4               | uint32    | Amount of faces             |
| 1               | uint16    | Amount of frames            |
| 1               | uint8     | Amount of data blocks       |

#### Base mesh structure

| Length in bytes | Data Type                | Description           |
|-----------------|--------------------------|-----------------------|
| 4               | float                    | Vertex X              |
| 4               | float                    | Vertex Y              |
| 4               | float                    | Vertex Z              |
| ...             | ...                      | ...                   |
| x               | depends on vertex amount | Connectivity Vertex 1 |
| x               | depends on vertex amount | Connectivity Vertex 2 |
| x               | depends on vertex amount | Connectivity Vertex 3 |
| ...             | ...                      | ...                   |

#### Data block structure

| Length in bytes | Data Type                    | Description                                        |
|-----------------|------------------------------|----------------------------------------------------|
| 1               | uint8                        | Length of the name                                 |
| n               | uint8                        | Name                                               |
| 1               | uint8                        | [Precision](#precision-values)                     |
| 1               | bool                         | Persistent (only one set of values for all frames) |
| n               | [depends](#precision-values) | Data                                               |

#### Precision values

| ID  | Data Type         |
|-----|-------------------|
| 0   | bool              |
| 1   | uint8             |
| 2   | uint16            |
| 3   | uint32            |
| 4   | uint64            |
| 5   | int8              |
| 6   | int16             |
| 7   | int32             |
| 8   | int64             |
| 9   | fp16              |
| 10  | fp32              |
| 11  | fp64              |
| 12  | 3 x uint8 (Color) |

#### Special names

| Name          | Usage                                                     |
|---------------|-----------------------------------------------------------|
| TL1, TL2, TL3 | Translate values for all vertices in x, y, z direction    |
| Col           | Vertex colors                                             |
| FPS           | Amount of frames per second for custom animations         |

</details>

### Single frame animation structure

<details>
  <summary>Detailed structure information</summary>

The first uint8 / byte defines the amount of single frame blocks. All faces are triangles.

### Single frame block

| Length in bytes | Data Type                | Description             |
|-----------------|--------------------------|-------------------------|
| 4               | uint32                   | Amount of vertices      |
| 4               | uint32                   | Amount of faces         |
| 4               | float                    | Vertex X                |
| 4               | float                    | Vertex Y                |
| 4               | float                    | Vertex Z                |
| ...             | ...                      | ...                     |
| 4               | depends on vertex amount | Connectivity Vertex 1   |
| 4               | depends on vertex amount | Connectivity Vertex 2   |
| 4               | depends on vertex amount | Connectivity Vertex 3   |
| ...             | ...                      | ...                     |
| 1               | uint8                    | Red color component 1   |
| 1               | uint8                    | Green color component 1 |
| 1               | uint8                    | Blue color component 1  |
| ...             | ...                      | ...                     |

</details>

### Rigid body animation structure

<details>
  <summary>Detailed structure information</summary>

Same header as the [default structure](#default-structure) with data blocks = amount of rigid body objects

#### Base mesh structure

| Length in bytes | Data Type                | Description             |
|-----------------|--------------------------|-------------------------|
| 4               | float                    | Vertex X                |
| 4               | float                    | Vertex Y                |
| 4               | float                    | Vertex Z                |
| ...             | ...                      | ...                     |
| 4               | depends on vertex amount | Connectivity Vertex 1   |
| 4               | depends on vertex amount | Connectivity Vertex 2   |
| 4               | depends on vertex amount | Connectivity Vertex 3   |
| ...             | ...                      | ...                     |
| 1               | uint8                    | Red color component 1   |
| 1               | uint8                    | Green color component 1 |
| 1               | uint8                    | Blue color component 1  |
| ...             | ...                      | ...                     |

#### Rigid body information

| Length in bytes | Data Type | Description                                                                  |
|-----------------|-----------|------------------------------------------------------------------------------|
| n * 4           | uint32    | Amount of vertices per rigid body object (starting from 0 / last rigid body) |

Each frame than starts with an uint8 that represents the amount
of [rigid body transform blocks](#rigid-body-transform-block) for this frame

#### Rigid body transform block

| Length in bytes | Data Type | Description                                            |
|-----------------|-----------|--------------------------------------------------------|
| 1               | uint8     | Rigid body object ID                                   |
| 1               | uint8     | [Transformation type](#rigid-body-transformation-type) |
| 4               | float     | Transformation value for x-axis                        |
| 4               | float     | Transformation value for y-axis                        |
| 4               | float     | Transformation value for z-axis                        |
| ...             | ...       | ...                                                    |

#### Rigid body transformation type

| ID  | Transformation |
|-----|----------------|
| 1xx | Position       |
| x1x | Rotation       |
| xx1 | Scale          |

</details>