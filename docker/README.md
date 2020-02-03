# Docker Instructions

To build the image:

```bash
docker build -t gtc2020_numba:latest .
```

The notebook image takes an optional build argument `BRANCH` to
select the branch or commit to checkout

```
docker build -t gtc2020_numba:latest --build-arg BRANCH=master .
```

Run the notebook with:

```bash
nvidia-docker run -p 9999:9999 -it gtc2020_numba:latest
```

It will start the jupyter notebook automatically.
