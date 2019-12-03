git:

- vendor/nxp-opensource/kernel-imx

- vendor/nxp-opensource/imx

feature:

- main(back) camera sensor ov5640

- camera flash light

note:

- camera sensor ov5640 is imx6 native supports CIS driver, however the supported features are relatively weak and not supported flash light.
- Use V4L2 int-device and i.MX ISP video architecture.