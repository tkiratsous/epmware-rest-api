# Export Module

Automate data extraction and file generation from EPMware systems.

## Overview

The Export module provides APIs for running export profiles/books, generating data extracts, and downloading exported files. 
It supports various formats including Excel, CSV, and custom delimited files for integration with downstream systems.



## Available Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/service/api/export/run` | POST | Execute export profile or book |
| `/service/api/export/download_file/{task_id}` | GET | Download exported file |
| `/service/api/export/get_execution_details` | GET | Get export execution history |



[← Back to Modules](../) | [Run Export](run-export/)
