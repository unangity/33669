# 33669

Reproduction for [Renovate discussion 33669](https://github.com/renovatebot/renovate/discussions/33669)

## Current behavior

UV processor is not able to use the configured pypi datasources because the implementation only set the UV extra index URLs when a list of updatedDeps is provided:
[39.114.0/lib/modules/manager/pep621/processors/uv.ts#L283-L300]() 


In the pyproject dependencies <https://github.com/unangity/33669/blob/main/pyproject.toml#L19>, `test_renovate` package is specified with `~=1.0`. `1.0.1` exists in the configued index (https://test.pypi.org/simple) as seen in <https://github.com/unangity/33669/actions/runs/13455855479/job/37599608672#step:4:14>. However, after configuring lockfileMaintenance in <https://github.com/unangity/33669/blob/main/renovate.json#L10-L13>, no pull request is created to update `1.0.0` in <https://github.com/unangity/33669/blob/main/uv.lock#L22> to `1.0.1`. This is because the configured datasource (in <https://github.com/unangity/33669/blob/main/renovate.json#L7>) is not set in the UV_EXTRA_INDEX_URL environment variable in <https://github.com/unangity/33669/actions/runs/13455855479/job/37599608672#step:5:972>.

This behaviour is probably because the `updatedDeps` property, which is used to set the UV_EXTRA_INDEX_URL for lock file maintenance in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/modules/manager/pep621/processors/uv.ts#L283-L300> is always empty (as seen in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/workers/repository/update/branch/get-updated.ts#L448>)

## Expected behavior

Since `https://test.pypi.org/simple` is set as a pypi datasource, it should be set in the UV_EXTRA_INDEX_URL. Also, since `1.0.1` exists in `https://test.pypi.org/simple` index, lockfileMaintenance should create a pull request updating `1.0.0` in <https://github.com/unangity/33669/blob/main/uv.lock#L22> to `1.0.1`.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/33669
