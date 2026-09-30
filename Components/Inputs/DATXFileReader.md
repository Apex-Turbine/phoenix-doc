## DATX File Reader
## Settings

- File (`file`)
  - Path to DATX file or DATX index file
  - Alias of `filename`
- Filename (`filename`)
  - Path to DATX file or DATX index file
  - Alias of `file`

___
## Phoenix API
___
### Description

Reads Dewesoft DATX data and publishes streams as vector data.

### I/O

Receives file selection settings.

Produces vector data streams discovered in the DATX file.

### JSON Setup Keys

Component specific global keys:
- file
  - Description: Alias of filename
  - Type: string
- filename
  - Description: The filename of the DATX or DATX Index file to open
  - Type: string
