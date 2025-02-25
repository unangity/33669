# 33669

Reproduction for [Renovate discussion 33669](https://github.com/renovatebot/renovate/discussions/33669)

## Current behavior
In the pyproject dependencies, `test_renovate` dependency is specified with `~=1.0` and `1.0.0` in the uv.lock file. `1.0.1` exists in the configued index (https://test.pypi.org/simple) as seen in <https://github.com/unangity/33669/actions/runs/13455855479/job/37599608672#step:4:14>. However, after running the renovate bot, the lockfileMaintenance PR does not update the `1.0.0` to `1.0.1` in the uv.lock file. The logs also shows that the UV_EXTRA_INDEX_URL is not set as an environment variable in the job which is a possible cause of the error.

This behaviour is probably because the `updatedDeps` property, which is used to set the UV_EXTRA_INDEX_URL for lock file maintenance in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/modules/manager/pep621/processors/uv.ts#L283-L300> is always empty (as seen in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/workers/repository/update/branch/get-updated.ts#L448>)

## Expected behavior

Since `https://test.pypi.org/simple` is set as a pypi datasource, it should be set in the UV_EXTRA_INDEX_URL in the logs. Also, since `1.0.1` of `test_renovate` package exists in `https://test.pypi.org/simple` index, lockfileMaintenance should create a pull request updating `1.0.0` in <https://github.com/unangity/33669/blob/main/uv.lock#L17> to `1.0.1`.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/33669
