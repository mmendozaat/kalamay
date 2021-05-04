wheel name format:

```
{name}-{version}(-{build tag})?-{python tag}-{abi tag}-{platform tag}.whl
```

where:

 - `name` and `version` from `setup.py`
 - `build tag` from `egg_info --tag-build=STRING`
 - `python tag` would be py3,
 - `abi tag` would be `none`,
 - `platform tag` would be `any`.
 - `abi tag` and `platform tag` are relevant when the package contains C extensions
