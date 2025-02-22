# 33669

Reproduction for [Renovate discussion 33669](https://github.com/renovatebot/renovate/discussions/33669)

## Current behavior

UV processor is not able to use the configured pypi datasources because the implementation only set the UV extra index URLs when a list of updatedDeps is provided:
[39.114.0/lib/modules/manager/pep621/processors/uv.ts#L283-L300]()

In the pyproject dependencies, `test_renovate` dependency is specified with `~=1.0` and `1.0.0` in the uv.lock file. `1.0.1` exists in the configued index (https://test.pypi.org/simple) as seen in <https://github.com/unangity/33669/actions/runs/13455855479/job/37599608672#step:4:14>. However, after running the renovate bot, the lockfileMaintenance PR does not update the `1.0.0` to `1.0.1` in the uv.lock file. The error can be found in <https://developer.mend.io/github/unangity/33669/-/job/64c66798-3e66-4c5d-8050-48d0340dfd57>. This also shows that the UV_EXTRA_INDEX_URL is not set as an environment variable in the job which is a possible cause of the error.

This behaviour is probably because the `updatedDeps` property, which is used to set the UV_EXTRA_INDEX_URL for lock file maintenance in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/modules/manager/pep621/processors/uv.ts#L283-L300> is always empty (as seen in <https://github.com/renovatebot/renovate/blob/39.114.0/lib/workers/repository/update/branch/get-updated.ts#L448>)

## Expected behavior

Since `https://test.pypi.org/simple` is set as a pypi datasource, it should be set in the UV_EXTRA_INDEX_URL. Also, since `1.0.1` exists in `https://test.pypi.org/simple` index, lockfileMaintenance should create a pull request updating `1.0.0` in <https://github.com/unangity/33669/blob/main/uv.lock#L22> to `1.0.1`.

This behaviour can be seen in the PR created in <https://github.com/unangity/33669/pull/4> created from successful job <https://developer.mend.io/github/unangity/33669/-/job/9995de2f-9f38-460d-b721-2fd2411fc563> when only a dependency (requests ~=2.32) from the default index (https://pypi.org) is updated because no UV_EXTRA_INDEX_URL is needed for dependencies from the default index.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/33669
