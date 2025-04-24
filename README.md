# Image Update Checking Action

This action checks to see if there are new packages available in the repositories of an image.
If there are, then it needs to be rebuilt, but only if it's base image is already up-to-date.

## Usage

This action is designed to be called from a GitHub workflow using the following format

```yaml
    steps:
      - name: Check updates
        id: check_update
        uses: alexiri/update-image-action@main
        with:
          imageref: ghcr.io/${{ github.repository_owner }}/${{ matrix.variant == 'gnome' && 'blueshift_v3' || 'blueshift_v3-plasma' }}
          baseref: quay.io/almalinuxorg/almalinux-bootc:10-kitten

      - name: Build Custom Image
        if: ${{ steps.check_update.outputs.rebuild-needed == 'true' }}
        uses: blue-build/github-action@v1.8
        with:
          ...
```

You can also force a rebuild by making this step conditional, like this:

```yaml
on:
  workflow_dispatch:
    inputs:
      FORCE_REBUILD:
        description: "Force rebuild even if no changes were made"
        required: false
        default: false
        type: boolean

...
    steps:
      - name: Check updates
        id: check_update
        if: ${{ ! inputs.FORCE_REBUILD }}
        uses: alexiri/update-image-action@main
        with:
          imageref: ghcr.io/${{ github.repository_owner }}/${{ matrix.variant == 'gnome' && 'blueshift_v3' || 'blueshift_v3-plasma' }}
          baseref: quay.io/almalinuxorg/almalinux-bootc:10-kitten

      - name: Build Custom Image
        if: ${{ steps.check_update.outputs.rebuild-needed == 'true' || inputs.FORCE_REBUILD }}
        uses: blue-build/github-action@v1.8
        with:
          ...
```
