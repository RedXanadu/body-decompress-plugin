# OWASP ModSecurity Core Rule Set - Body Decompress Plugin

## Description

This is a plugin that brings on-the-fly decompression of `RESPONSE_BODY` to CRS.

As some of the malicious software is using compression of response body to
bypass detection by checking the `RESPONSE_BODY` for patterns, this plugin will
help you to prevent this.

Decompression is performed using a bundled Lua script when:
 * response has `Content-Encoding` header which contains any of `gzip`, `compress`, `deflate`
 * `RESPONSE_BODY` starts with gzip magic number

Decompressed response body is then available via variable `TX:RESPONSE_BODY_DECOMPRESSED`.

Currently, only gzip decompression is supported.

## Prerequisities

 * CRS version 3.4 or newer (or see "Preparation for older installations" below)
 * ModSecurity compiled with Lua support
 * lua-zlib library

## Installation

Copy all files from `plugins` directory into the `plugins` directory of your
OWASP ModSecurity Core Rule Set (CRS) installation.


### Preparation for older installations

* Create a folder named `plugins` in your existing CRS installation. That folder
  is meant to be on the same level as the `rules` folder. So there is your
  `crs-setup.conf` file and next to it the two folders `rules` and `plugins`.
* Update your CRS rules include to follow this pattern:

```
<IfModule security2_module>
 Include modsecurity.d/owasp-modsecurity-crs/crs-setup.conf

 IncludeOptional modsecurity.d/owasp-modsecurity-crs/plugins/*-before.conf
 Include modsecurity.d/owasp-modsecurity-crs/rules/*.conf
 IncludeOptional modsecurity.d/owasp-modsecurity-crs/plugins/*-after.conf

</IfModule>
```

_Your exact config may look a bit different, namely the paths. The important part is to accompany the rules-include with two plugins-includes before and after like above. Adjust the paths accordingly._

## Configuration

All settings can be done in file `plugins/body-decompress-config-before.conf`.

### tx.body-decompress-plugin_enable

This setting can be used to disable or enable the whole plugin.

Values:
 * 0 - disable plugin
 * 1 - enable plugin

Default value: 1
 
### tx.body-decompress-plugin_max_data_size_bytes

Maximum data size, in bytes, which are decompressed. If (compressed) data are
bigger, decompression is skipped. This option is available to lower chances of
successfull decompression bomb attack. Do NOT set this too high as decompression
is done inside RAM.

Default value: 102400

## Known problems

 * Web browsers are printing `Content Encoding Error` while blocking request
   which uses compression, as `Content-Encoding` header is still set and browser
   awaits compressed response. This problem is only occuring when PHP is running
   using FastCGI (PHP-FPM).

## License

Copyright (c) 2021 OWASP ModSecurity Core Rule Set project. All rights reserved.

The OWASP ModSecurity Core Rule Set and its official plugins are distributed
under Apache Software License (ASL) version 2. Please see the enclosed LICENSE
file for full details.
