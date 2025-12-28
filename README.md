# Heroku Buildpack for libheif 1.18.0+

This buildpack compiles and installs libheif 1.18.0+ from source on Heroku. This is necessary because Ubuntu repositories only provide libheif 1.17.6, which doesn't support iOS 18+ HEIC files with multiple auxiliary images.

## Why This Buildpack?

- Ubuntu repositories only have libheif 1.17.6
- iOS 18+ HEIC files require libheif 1.18.0+ to handle:
  - Multiple auxiliary image references
  - Complex metadata structures
- The standard vips buildpack uses apt packages, which are too old

## Usage

Add this buildpack **before** the vips buildpack:

```bash
heroku buildpacks:add --index 1 https://github.com/your-username/heroku-buildpack-libheif
heroku buildpacks:add --index 2 https://github.com/brandoncc/heroku-buildpack-vips
heroku buildpacks:add --index 3 heroku/ruby
```

## What It Does

1. Installs build dependencies (cmake, gcc, etc.)
2. Downloads libheif 1.18.2 source code
3. Compiles libheif from source
4. Installs to `/app/vendor/libheif`
5. Sets up environment variables (LD_LIBRARY_PATH, PKG_CONFIG_PATH)
6. Cleans up build dependencies

## Verification

After deployment, verify the installation:

```bash
heroku run "dpkg -l | grep libheif" -a your-app-name
# Should show libheif 1.18.2 or higher

heroku run "ls -la /app/vendor/libheif/lib/" -a your-app-name
# Should show libheif.so files
```

## Requirements

- Heroku stack: Heroku-22 or Heroku-24
- Build time: ~5-10 minutes (compilation from source)
- Slug size: Adds ~5-10MB

## License

Same as libheif (LGPL-3.0)

