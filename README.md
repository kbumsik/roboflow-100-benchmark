# Roboflow 100 📸: A Rich, Multi-Domain Object Detection Benchmark

![rf100blog-mosaicthing](https://user-images.githubusercontent.com/15908060/202452898-9ca6b8f7-4805-4e8e-949a-6e080d7b94d2.jpg)

This repository implements the Roboflow 100 benchmark developed by [Roboflow](https://roboflow.com/). It contains code to download the dataset and reproduce
mAP values for YOLOv5 and YOLOv7 Fine-Tuning and GLIP Evaluation on 100 of Roboflow Universe
datasets.


## RF100

`RF100` contains the following datasets, carefully chosen from more than 90'000 datasets hosted on our [universe hub](https://universe.roboflow.com/). The datasets are splitted in 7 categories: `Aerial`, `Videogames`, `Microscopic`, `Underwater`, `Documents`, `Electromagnetic` and `Real World`. 


| Category        | Datasets | Images  | Classes |
|-----------------|----------|---------|---------|
| Aerial          | 7        | 9683    | 24      |
| Videogames      | 7        | 11579   | 88      |
| Microscopic     | 11       | 13378   | 28      |
| Underwater      | 5        | 18003   | 39      |
| Documents       | 8        | 24813   | 90      |
| Electromagnetic | 12       | 36381   | 41      |
| Real World      | 50       | 110615  | 495     |
| **Total**           | **100**      | **224,714** | **805**     |


🚨 **Head over the [Appendix](#appendix) at the bottom to see samples from each dataset**

We provide a [notebook to help you using RF100 with PyTorch](/notebooks/roboflow-100-pytorch.ipynb)

## Getting Started

First, clone this repo and go inside it.

```bash
git clone https://github.com/roboflow-ai/roboflow-100-benchmark.git
cd roboflow-100-benchmark
git submodule update --init --recursive
```

You will need an API key. `RF100` can be accessed with any key from Roboflow, head over [our doc](https://docs.roboflow.com/rest-api.') to learn how to get one.

Then, export the key to your current shell

```bash
export ROBOFLOW_API_KEY=<YOUR_API_KEY>
```

**Note**: The datasets are taken from `datasets_links.txt`, you can modify that file to add/remove datasets.

### Docker

The easiest and faster way to download `RF100` is using [docker](https://docs.docker.com/engine/install/) and our [Dockerfile](Dockerfile.rf100.download).

**NOTE** Be sure to do the [post process steps](https://docs.docker.com/engine/install/linux-postinstall/) after you installed docker, we will read and write to shared volumes so your user should also be in the docker group.

If you have an NVIDIA GPU, be sure to also install [nvidia docker](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)

First, build the container

```bash
docker build -t rf100-download -f Dockerfile.rf100.download .
```

Be sure to have the `ROBOFLOW_API_KEY` in your env, then run it

```bash
docker run --rm -it \
    -e ROBOFLOW_API_KEY=$ROBOFLOW_API_KEY \
    -v $(pwd)/rf100:/workspace/rf100 \
    rf100-download
```

Internally, `RF100` will downloaded to `/app/rf100`. You can also specify the format with the `-f` flag, by default `coco` is used.

```bash
docker run --rm -it \
    -e ROBOFLOW_API_KEY=$ROBOFLOW_API_KEY \
    -v ${PWD}/rf100:/workspace/rf100 \
    rf100-download -f yolov5
```

### Local Env

To download `RF100` in your local enviroment (python `>=3.6`), you need to install roboflow

```bash
pip install roboflow
```

Then,

```bash
chmod 770 ./scripts/download_datasets.sh
./scripts/download_datasets.sh
./scripts/download_datasets.sh -f yolov5 $ change format
./scripts/download_datasets.sh -l <path_to_my_location> change download location
```

### Formats

Supported formats are

- `coco`
- `yolov5` (used by YOLOv7 as well)

## Reproduce Results

We will use docker to ensure the same enviroment is used.

First, build the container

```
docker build -t rf100-benchmark -f Dockerfile.rf100.benchmark .
```

Then, follow the guide for each model.

All results are stored inside `./runs`.

### [YOLOv5](https://github.com/ultralytics/yolov5) Fine-Tuning

**Note**, we will map the current folder to the container file system to persist data

```bash
nvidia-docker run --gpus all --rm -it --ipc host --network host --shm-size 64g \
    -e ROBOFLOW_API_KEY=$ROBOFLOW_API_KEY \
    -v /etc/group:/etc/group:ro \
    -u "$(id -u):$(id -g)" \
    -v ${PWD}/runs:/workspace/runs \
    -v ${PWD}/datasets_links_640.txt:/workspace/datasets_links_640.txt \
    rf100-benchmark ./yolov5-benchmark/train.sh
```

### [YOLOv7](https://github.com/WongKinYiu/yolov7) Fine-Tuning

**Note**, we will map the current folder to the container file system to persist data

```bash
nvidia-docker run --gpus all --rm -d --ipc host --network host --shm-size 64g \
    -e ROBOFLOW_API_KEY=$ROBOFLOW_API_KEY \
    -v /etc/group:/etc/group:ro \
    -u "$(id -u):$(id -g)" \
    -v ${PWD}/runs:/workspace/runs \
    -v ${PWD}/datasets_links_640.txt:/workspace/datasets_links_640.txt \
    rf100-benchmark ./yolov7-benchmark/train.sh
```

### [GLIP](https://github.com/microsoft/GLIP)

```bash
nvidia-docker run --gpus all --rm -it --ipc host --network host --shm-size 64g \
    -e ROBOFLOW_API_KEY=$ROBOFLOW_API_KEY \
    -v /etc/group:/etc/group:ro \
    -u "$(id -u):$(id -g)" \
    -v ${PWD}/runs:/workspace/runs \
    -v ${PWD}/datasets_links_640.txt:/workspace/datasets_links_640.txt \
    rf100-benchmark ./GLIP-benchmark/train.sh
```


## Appendix

| dataset                                                                                                   | samples                                                   |
|:----------------------------------------------------------------------------------------------------------|:----------------------------------------------------------|
| [hand-gestures-jps7z](https://universe.roboflow.com/roboflow-100/hand-gestures-jps7z)                     | ![alt](doc/images/grid/hand-gestures-jps7z.jpg)           |
| [smoke-uvylj](https://universe.roboflow.com/roboflow-100/smoke-uvylj)                                     | ![alt](doc/images/grid/smoke-uvylj.jpg)                   |
| [wall-damage](https://universe.roboflow.com/roboflow-100/wall-damage)                                     | ![alt](doc/images/grid/wall-damage.jpg)                   |
| [corrosion-bi3q3](https://universe.roboflow.com/roboflow-100/corrosion-bi3q3)                             | ![alt](doc/images/grid/corrosion-bi3q3.jpg)               |
| [excavators-czvg9](https://universe.roboflow.com/roboflow-100/excavators-czvg9)                           | ![alt](doc/images/grid/excavators-czvg9.jpg)              |
| [chess-pieces-mjzgj](https://universe.roboflow.com/roboflow-100/chess-pieces-mjzgj)                       | ![alt](doc/images/grid/chess-pieces-mjzgj.jpg)            |
| [road-signs-6ih4y](https://universe.roboflow.com/roboflow-100/road-signs-6ih4y)                           | ![alt](doc/images/grid/road-signs-6ih4y.jpg)              |
| [street-work](https://universe.roboflow.com/roboflow-100/street-work)                                     | ![alt](doc/images/grid/street-work.jpg)                   |
| [construction-safety-gsnvb](https://universe.roboflow.com/roboflow-100/construction-safety-gsnvb)         | ![alt](doc/images/grid/construction-safety-gsnvb.jpg)     |
| [road-traffic](https://universe.roboflow.com/roboflow-100/road-traffic)                                   | ![alt](doc/images/grid/road-traffic.jpg)                  |
| [washroom-rf1fa](https://universe.roboflow.com/roboflow-100/washroom-rf1fa)                               | ![alt](doc/images/grid/washroom-rf1fa.jpg)                |
| [circuit-elements](https://universe.roboflow.com/roboflow-100/circuit-elements)                           | ![alt](doc/images/grid/circuit-elements.jpg)              |
| [mask-wearing-608pr](https://universe.roboflow.com/roboflow-100/mask-wearing-608pr)                       | ![alt](doc/images/grid/mask-wearing-608pr.jpg)            |
| [cables-nl42k](https://universe.roboflow.com/roboflow-100/cables-nl42k)                                   | ![alt](doc/images/grid/cables-nl42k.jpg)                  |
| [soda-bottles](https://universe.roboflow.com/roboflow-100/soda-bottles)                                   | ![alt](doc/images/grid/soda-bottles.jpg)                  |
| [truck-movement](https://universe.roboflow.com/roboflow-100/truck-movement)                               | ![alt](doc/images/grid/truck-movement.jpg)                |
| [wine-labels](https://universe.roboflow.com/roboflow-100/wine-labels)                                     | ![alt](doc/images/grid/wine-labels.jpg)                   |
| [digits-t2eg6](https://universe.roboflow.com/roboflow-100/digits-t2eg6)                                   | ![alt](doc/images/grid/digits-t2eg6.jpg)                  |
| [vehicles-q0x2v](https://universe.roboflow.com/roboflow-100/vehicles-q0x2v)                               | ![alt](doc/images/grid/vehicles-q0x2v.jpg)                |
| [peanuts-sd4kf](https://universe.roboflow.com/roboflow-100/peanuts-sd4kf)                                 | ![alt](doc/images/grid/peanuts-sd4kf.jpg)                 |
| [printed-circuit-board](https://universe.roboflow.com/roboflow-100/printed-circuit-board)                 | ![alt](doc/images/grid/printed-circuit-board.jpg)         |
| [pests-2xlvx](https://universe.roboflow.com/roboflow-100/pests-2xlvx)                                     | ![alt](doc/images/grid/pests-2xlvx.jpg)                   |
| [cavity-rs0uf](https://universe.roboflow.com/roboflow-100/cavity-rs0uf)                                   | ![alt](doc/images/grid/cavity-rs0uf.jpg)                  |
| [leaf-disease-nsdsr](https://universe.roboflow.com/roboflow-100/leaf-disease-nsdsr)                       | ![alt](doc/images/grid/leaf-disease-nsdsr.jpg)            |
| [marbles](https://universe.roboflow.com/roboflow-100/marbles)                                             | ![alt](doc/images/grid/marbles.jpg)                       |
| [pills-sxdht](https://universe.roboflow.com/roboflow-100/pills-sxdht)                                     | ![alt](doc/images/grid/pills-sxdht.jpg)                   |
| [poker-cards-cxcvz](https://universe.roboflow.com/roboflow-100/poker-cards-cxcvz)                         | ![alt](doc/images/grid/poker-cards-cxcvz.jpg)             |
| [number-ops](https://universe.roboflow.com/roboflow-100/number-ops)                                       | ![alt](doc/images/grid/number-ops.jpg)                    |
| [insects-mytwu](https://universe.roboflow.com/roboflow-100/insects-mytwu)                                 | ![alt](doc/images/grid/insects-mytwu.jpg)                 |
| [cotton-20xz5](https://universe.roboflow.com/roboflow-100/cotton-20xz5)                                   | ![alt](doc/images/grid/cotton-20xz5.jpg)                  |
| [furniture-ngpea](https://universe.roboflow.com/roboflow-100/furniture-ngpea)                             | ![alt](doc/images/grid/furniture-ngpea.jpg)               |
| [cable-damage](https://universe.roboflow.com/roboflow-100/cable-damage)                                   | ![alt](doc/images/grid/cable-damage.jpg)                  |
| [animals-ij5d2](https://universe.roboflow.com/roboflow-100/animals-ij5d2)                                 | ![alt](doc/images/grid/animals-ij5d2.jpg)                 |
| [coins-1apki](https://universe.roboflow.com/roboflow-100/coins-1apki)                                     | ![alt](doc/images/grid/coins-1apki.jpg)                   |
| [apples-fvpl5](https://universe.roboflow.com/roboflow-100/apples-fvpl5)                                   | ![alt](doc/images/grid/apples-fvpl5.jpg)                  |
| [people-in-paintings](https://universe.roboflow.com/roboflow-100/people-in-paintings)                     | ![alt](doc/images/grid/people-in-paintings.jpg)           |
| [circuit-voltages](https://universe.roboflow.com/roboflow-100/circuit-voltages)                           | ![alt](doc/images/grid/circuit-voltages.jpg)              |
| [uno-deck](https://universe.roboflow.com/roboflow-100/uno-deck)                                           | ![alt](doc/images/grid/uno-deck.jpg)                      |
| [grass-weeds](https://universe.roboflow.com/roboflow-100/grass-weeds)                                     | ![alt](doc/images/grid/grass-weeds.jpg)                   |
| [gauge-u2lwv](https://universe.roboflow.com/roboflow-100/gauge-u2lwv)                                     | ![alt](doc/images/grid/gauge-u2lwv.jpg)                   |
| [sign-language-sokdr](https://universe.roboflow.com/roboflow-100/sign-language-sokdr)                     | ![alt](doc/images/grid/sign-language-sokdr.jpg)           |
| [valentines-chocolate](https://universe.roboflow.com/roboflow-100/valentines-chocolate)                   | ![alt](doc/images/grid/valentines-chocolate.jpg)          |
| [fish-market-ggjso](https://universe.roboflow.com/roboflow-100/fish-market-ggjso)                         | ![alt](doc/images/grid/fish-market-ggjso.jpg)             |
| [lettuce-pallets](https://universe.roboflow.com/roboflow-100/lettuce-pallets)                             | ![alt](doc/images/grid/lettuce-pallets.jpg)               |
| [shark-teeth-5atku](https://universe.roboflow.com/roboflow-100/shark-teeth-5atku)                         | ![alt](doc/images/grid/shark-teeth-5atku.jpg)             |
| [bees-jt5in](https://universe.roboflow.com/roboflow-100/bees-jt5in)                                       | ![alt](doc/images/grid/bees-jt5in.jpg)                    |
| [sedimentary-features-9eosf](https://universe.roboflow.com/roboflow-100/sedimentary-features-9eosf)       | ![alt](doc/images/grid/sedimentary-features-9eosf.jpg)    |
| [currency-v4f8j](https://universe.roboflow.com/roboflow-100/currency-v4f8j)                               | ![alt](doc/images/grid/currency-v4f8j.jpg)                |
| [trail-camera](https://universe.roboflow.com/roboflow-100/trail-camera)                                   | ![alt](doc/images/grid/trail-camera.jpg)                  |
| [cell-towers](https://universe.roboflow.com/roboflow-100/cell-towers)                                     | ![alt](doc/images/grid/cell-towers.jpg)                   |
| [apex-videogame](https://universe.roboflow.com/roboflow-100/apex-videogame)                               | ![alt](doc/images/grid/apex-videogame.jpg)                |
| [farcry6-videogame](https://universe.roboflow.com/roboflow-100/farcry6-videogame)                         | ![alt](doc/images/grid/farcry6-videogame.jpg)             |
| [csgo-videogame](https://universe.roboflow.com/roboflow-100/csgo-videogame)                               | ![alt](doc/images/grid/csgo-videogame.jpg)                |
| [avatar-recognition-nuexe](https://universe.roboflow.com/roboflow-100/avatar-recognition-nuexe)           | ![alt](doc/images/grid/avatar-recognition-nuexe.jpg)      |
| [halo-infinite-angel-videogame](https://universe.roboflow.com/roboflow-100/halo-infinite-angel-videogame) | ![alt](doc/images/grid/halo-infinite-angel-videogame.jpg) |
| [team-fight-tactics](https://universe.roboflow.com/roboflow-100/team-fight-tactics)                       | ![alt](doc/images/grid/team-fight-tactics.jpg)            |
| [robomasters-285km](https://universe.roboflow.com/roboflow-100/robomasters-285km)                         | ![alt](doc/images/grid/robomasters-285km.jpg)             |
| [tweeter-posts](https://universe.roboflow.com/roboflow-100/tweeter-posts)                                 | ![alt](doc/images/grid/tweeter-posts.jpg)                 |
| [tweeter-profile](https://universe.roboflow.com/roboflow-100/tweeter-profile)                             | ![alt](doc/images/grid/tweeter-profile.jpg)               |
| [document-parts](https://universe.roboflow.com/roboflow-100/document-parts)                               | ![alt](doc/images/grid/document-parts.jpg)                |
| [activity-diagrams-qdobr](https://universe.roboflow.com/roboflow-100/activity-diagrams-qdobr)             | ![alt](doc/images/grid/activity-diagrams-qdobr.jpg)       |
| [signatures-xc8up](https://universe.roboflow.com/roboflow-100/signatures-xc8up)                           | ![alt](doc/images/grid/signatures-xc8up.jpg)              |
| [paper-parts](https://universe.roboflow.com/roboflow-100/paper-parts)                                     | ![alt](doc/images/grid/paper-parts.jpg)                   |
| [tabular-data-wf9uh](https://universe.roboflow.com/roboflow-100/tabular-data-wf9uh)                       | ![alt](doc/images/grid/tabular-data-wf9uh.jpg)            |
| [paragraphs-co84b](https://universe.roboflow.com/roboflow-100/paragraphs-co84b)                           | ![alt](doc/images/grid/paragraphs-co84b.jpg)              |
| [underwater-pipes-4ng4t](https://universe.roboflow.com/roboflow-100/underwater-pipes-4ng4t)               | ![alt](doc/images/grid/underwater-pipes-4ng4t.jpg)        |
| [aquarium-qlnqy](https://universe.roboflow.com/roboflow-100/aquarium-qlnqy)                               | ![alt](doc/images/grid/aquarium-qlnqy.jpg)                |
| [peixos-fish](https://universe.roboflow.com/roboflow-100/peixos-fish)                                     | ![alt](doc/images/grid/peixos-fish.jpg)                   |
| [underwater-objects-5v7p8](https://universe.roboflow.com/roboflow-100/underwater-objects-5v7p8)           | ![alt](doc/images/grid/underwater-objects-5v7p8.jpg)      |
| [coral-lwptl](https://universe.roboflow.com/roboflow-100/coral-lwptl)                                     | ![alt](doc/images/grid/coral-lwptl.jpg)                   |
| [aerial-pool](https://universe.roboflow.com/roboflow-100/aerial-pool)                                     | ![alt](doc/images/grid/aerial-pool.jpg)                   |
| [secondary-chains](https://universe.roboflow.com/roboflow-100/secondary-chains)                           | ![alt](doc/images/grid/secondary-chains.jpg)              |
| [aerial-spheres](https://universe.roboflow.com/roboflow-100/aerial-spheres)                               | ![alt](doc/images/grid/aerial-spheres.jpg)                |
| [soccer-players-5fuqs](https://universe.roboflow.com/roboflow-100/soccer-players-5fuqs)                   | ![alt](doc/images/grid/soccer-players-5fuqs.jpg)          |
| [weed-crop-aerial](https://universe.roboflow.com/roboflow-100/weed-crop-aerial)                           | ![alt](doc/images/grid/weed-crop-aerial.jpg)              |
| [aerial-cows](https://universe.roboflow.com/roboflow-100/aerial-cows)                                     | ![alt](doc/images/grid/aerial-cows.jpg)                   |
| [cloud-types](https://universe.roboflow.com/roboflow-100/cloud-types)                                     | ![alt](doc/images/grid/cloud-types.jpg)                   |
| [stomata-cells](https://universe.roboflow.com/roboflow-100/stomata-cells)                                 | ![alt](doc/images/grid/stomata-cells.jpg)                 |
| [bccd-ouzjz](https://universe.roboflow.com/roboflow-100/bccd-ouzjz)                                       | ![alt](doc/images/grid/bccd-ouzjz.jpg)                    |
| [parasites-1s07h](https://universe.roboflow.com/roboflow-100/parasites-1s07h)                             | ![alt](doc/images/grid/parasites-1s07h.jpg)               |
| [cells-uyemf](https://universe.roboflow.com/roboflow-100/cells-uyemf)                                     | ![alt](doc/images/grid/cells-uyemf.jpg)                   |
| [4-fold-defect](https://universe.roboflow.com/roboflow-100/4-fold-defect)                                 | ![alt](doc/images/grid/4-fold-defect.jpg)                 |
| [bacteria-ptywi](https://universe.roboflow.com/roboflow-100/bacteria-ptywi)                               | ![alt](doc/images/grid/bacteria-ptywi.jpg)                |
| [cotton-plant-disease](https://universe.roboflow.com/roboflow-100/cotton-plant-disease)                   | ![alt](doc/images/grid/cotton-plant-disease.jpg)          |
| [mitosis-gjs3g](https://universe.roboflow.com/roboflow-100/mitosis-gjs3g)                                 | ![alt](doc/images/grid/mitosis-gjs3g.jpg)                 |
| [phages](https://universe.roboflow.com/roboflow-100/phages)                                               | ![alt](doc/images/grid/phages.jpg)                        |
| [liver-disease](https://universe.roboflow.com/roboflow-100/liver-disease)                                 | ![alt](doc/images/grid/liver-disease.jpg)                 |
| [asbestos](https://universe.roboflow.com/roboflow-100/asbestos)                                           | ![alt](doc/images/grid/asbestos.jpg)                      |
| [thermal-dogs-and-people-x6ejw](https://universe.roboflow.com/roboflow-100/thermal-dogs-and-people-x6ejw) | ![alt](doc/images/grid/thermal-dogs-and-people-x6ejw.jpg) |
| [solar-panels-taxvb](https://universe.roboflow.com/roboflow-100/solar-panels-taxvb)                       | ![alt](doc/images/grid/solar-panels-taxvb.jpg)            |
| [radio-signal](https://universe.roboflow.com/roboflow-100/radio-signal)                                   | ![alt](doc/images/grid/radio-signal.jpg)                  |
| [thermal-cheetah-my4dp](https://universe.roboflow.com/roboflow-100/thermal-cheetah-my4dp)                 | ![alt](doc/images/grid/thermal-cheetah-my4dp.jpg)         |
| [x-ray-rheumatology](https://universe.roboflow.com/roboflow-100/x-ray-rheumatology)                       | ![alt](doc/images/grid/x-ray-rheumatology.jpg)            |
| [acl-x-ray](https://universe.roboflow.com/roboflow-100/acl-x-ray)                                         | ![alt](doc/images/grid/acl-x-ray.jpg)                     |
| [abdomen-mri](https://universe.roboflow.com/roboflow-100/abdomen-mri)                                     | ![alt](doc/images/grid/abdomen-mri.jpg)                   |
| [axial-mri](https://universe.roboflow.com/roboflow-100/axial-mri)                                         | ![alt](doc/images/grid/axial-mri.jpg)                     |
| [gynecology-mri](https://universe.roboflow.com/roboflow-100/gynecology-mri)                               | ![alt](doc/images/grid/gynecology-mri.jpg)                |
| [brain-tumor-m2pbp](https://universe.roboflow.com/roboflow-100/brain-tumor-m2pbp)                         | ![alt](doc/images/grid/brain-tumor-m2pbp.jpg)             |
| [bone-fracture-7fylg](https://universe.roboflow.com/roboflow-100/bone-fracture-7fylg)                     | ![alt](doc/images/grid/bone-fracture-7fylg.jpg)           |
| [flir-camera-objects](https://universe.roboflow.com/roboflow-100/flir-camera-objects)                     | ![alt](doc/images/grid/flir-camera-objects.jpg)           |
