# Avatars

```raw
https://api.lavaboom.io/avatars/9bd33e51aed4faea99e86b9b74e50b87.png?width=150
https://api.lavaboom.io/avatars/b4a8b619161ecec5105f56738ff065ca.svg
```

The API contains a simple avatar generator. In order to see an avatar generated
for someone's email, use the following URL scheme:

`https://[API URL]/avatars/[MD5 hash of an email].(png|svg)`

You can also define the desired avatar's width by specifying a `width` query
parameter.

### Examples

![PNG example](https://api.lavaboom.io/avatars/9bd33e51aed4faea99e86b9b74e50b87.png)
![SVG example](https://api.lavaboom.io/avatars/b4a8b619161ecec5105f56738ff065ca.svg)