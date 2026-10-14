# Configuration Generator

A lightweight, browser-based tool for generating bulk configuration files from Jinja2 templates (`.j2`, `.jinja`, `.tmpl`, `.txt`, etc.). 

This tool parses your templates for variables, provides a spreadsheet-like interface for data entry, and exports the rendered configurations individually or as a bulk ZIP archive. It runs entirely locally in your browser—no server required.

## Key Features

* **Local & Secure:** All file parsing and generation happens client-side in your browser. No data is sent to an external server.
* **Smart Folder Sync:** Supports the modern File System Access API (Chrome/Edge) to remember your template directory across sessions.
* **Automatic Table Generation:** Automatically detects `{{ variable }}` syntax in your templates and generates a data-entry table.
* **Intelligent Validation:** Built-in validation rules optimized for networking configurations (IPv4, subnet masks, VLANs, ASNs, Cisco interface names, etc.).
* **CSV Import/Export:** Easily export your variable headers to a CSV, fill it out in Excel, and import it back for bulk generation.
* **Bulk Export:** Generate dozens of configuration files at once and download them in a single `.zip` file.

## Getting Started

1. **Open the Tool:** Simply open `Configuration_Generator.html` in any modern web browser.
2. **Load a Template:**
   * **Drag and Drop:** Drag a single template file directly onto the dashed drop zone.
   * **Choose Folder:** Click "Choose Template Folder" to connect a local directory containing your templates.
3. **Fill in the Variables:**
   * The tool will scan the template and create a table column for every unique variable it finds.
   * Enter your values row by row. Each row represents a distinct output file.
   * Use the **Add Row** button to duplicate the row above for faster data entry.
4. **Generate Configurations:**
   * Click **&#8681; .txt** on a specific row to download just that file.
   * Click **Generate & Download ZIP** to download a zipped archive of all rows.

## How Template Parsing Works

The generator looks for standard Jinja2 variable interpolation: `{{ VARIABLE_NAME }}`. 

### Defaults
It also supports inline defaults: `{{ VARIABLE_NAME | default('my_default') }}`. 

If you want to control the exact order of columns in the table, you can declare your variables at the top of your template using `set` blocks. The tool will parse these, set the table columns in this order, and strip these lines from the final output:
```jinja2
{%- set HOSTNAME = HOSTNAME | default('Router-1') -%}
{%- set MGMT_IP = MGMT_IP | default('192.168.1.1') -%}
```

## Smart Validation Note

While this tool can parse **any generic Jinja2 file**, it includes "smart" validation checks that are heavily optimized for Cisco and networking configurations. 

The tool classifies variables based on their names. If you are using generic templates, be aware of the following naming triggers:
* **IP/MASK:** Variables ending in `_IP`, `_MASK`, or `_GW` will be strictly validated as IPv4 addresses or Subnet Masks.
* **HOSTNAME:** Variables named `HOSTNAME` must adhere to Cisco hostname rules (no spaces, starts with a letter, max 63 chars).
* **INTERFACE:** Variables containing `INTERFACE` or `PORT` (e.g., `_PORT_1`) will be checked against common network interface conventions (e.g., `GigabitEthernet0/1`).
* **VLAN/ASN:** Variables containing `VLAN` or `ASN` will be restricted to valid integer ranges.

If a field turns red, hover over it to see the specific validation error. 

## Browser Compatibility

* **Google Chrome / Microsoft Edge:** Full feature support, including persistent folder access.
* **Firefox / Safari:** Fully functional via standard file picker and drag-and-drop, though persistent folder access is not supported by these browsers.
