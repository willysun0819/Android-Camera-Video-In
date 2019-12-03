git:

- vendor/nxp-opensource/kernel-imx



feature:

- main(back) camera sensor ov5640


note:

- camera sensor ov5640 is imx6 native supports CIS driver, however the supported features are relatively weak and not supported flash light.
- Use V4L2 sub-devices and introduce the media controller framework, the IMX ISI V4L2 video media-entity graph architecture.
