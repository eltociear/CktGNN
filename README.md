# CktGNN
CktGNN is a two-level GNN model with a pre-designed subgraph basis for the analog circuit (DAG) encoding. CktGNN simultaneously optimizes circuit topology and device features, achieving state-of-art performance in analog circuit optimization. <br/>Source code for ICLR 2022 paper: 'CktGNN: Circuit Graph Neural Network for Electronic Design Automation' (https://openreview.net/forum?id=NE2911Kq1sp).


## OCB: Open Circuit Benchmark

* OCB is the first open benchmark dataset for analog circuits (i.e. operational amplifiers (Op-Amps)), equipped with circuit generation code, evaluation code, and transformation code that convert simulation results to different graph datasets (i.e. igraph, ptgraph, tensor.) Currently, OCB collects two datasets, Ckt-Bench-101 and Ckt-Bench-301 for the general-prpose graph learning tasks on circuits. 

* Ckt-Bench-101 (directory: `/OCB/CktBench101`): Ckt-Bench-101 is generated based on the dataset used in our ICLR 2022 paper. Ckt-Bench-101 contains 10000 different circuits, and it eliminates invalid circuits in the datasets used in the ICLR 2022 paper and replace them with new valid simulations. 

* Ckt-Bench-301 (directory: `/OCB/CktBench301`): Ckt-Bench-301 contains 50000 circuits. Circuits in Ckt-Bench-301 and in Ckt-Bench-101 have different combinations of device features and circuit topology. This benchmark dataset is proposed to perform the Bayesian optimization for the machine learning community, as it is not straightforward to implement simulation code on the circuit simulators without relevant expertise.

* Source code (directory: `/OCB/src`): The source codes enable users to construct their own analog circuit datasets of arbitrary size. The folder 'simulator' provides simulation codes for circuit simulators. [circuit_generation.py](circuit_generation.py) generates circuits and weite them in .txt file. [utils_src.py](utils_src.py) includes functions (train_test_generator_topo_simple) that convert the txt circuits and relevant simulation results to igraph data.


## Environment
* Tested with Python 3.7, PyTorch 1.8.0, and PyTorch Geometric 1.7.2
* Other required packages are listed in [requirements.txt](requirements.txt)
* The experiment codes are basically constructed upon [D-VAE](https://github.com/muhanzhang/D-VAE/).

## Experiments on Ckt-Bench-101

* Run variatioal auto-encoders on Ckt-Bench-101 to train different circuit/DAG encoders (CktGNN, PACE, DVAE, DAGNN...), and test the decoding ability of the decoder in the VAE (proportion of valid DAGs, valid circuits, novel DAGs generated from the latent encoding space of the circuit encoder, proportion of accurately reconstructed DAGs.) 

`python main.py --epochs 300 --save_appendix _cktgnn --model CktGNN --hs 301`

User can select different models (e.g. DVAE, DAGNN ..) and uses the corresponding save appendix (e.g. `--save_appendix _dvae`, `--save_appendix _dagnn` ) to store the results.

* Run SGP regression to test whether the circuit encoder can generate a smooth latent space w.r.t. circuit properties. There are many circuit properties: FoM, Gain, Bw, Pm, while FoM is the most critical one. In circuit optimization, FoM characterizes the circuit quality and the obective is to maximize circuit's FoM.

`python sgp_regression.py --checkpoint 300 --save_appendix _cktgnn --model CktGNN --hs 301`

Make sure that `--save_appendix --model --hs` are consistent with previous experiment, and `--checkpoint` equals to the `--epoch`.

## Experiments on Ckt-Bench-301

* Two bayesian optimization methods are provided in directory `/search_methods`: DNGO and Bohamiann. These codes are based on [pybnn](https://github.com/automl/pybnn/tree/master)

* Implement Bayesian optimization on Ckt-Bench-301 to find the optimal circuits with the higest FoM. 

First, run `python inference.py --checkpoint 300 -save-appendix _cktgnn --model CktGNN --hs 301 ` to generate the embeddings in the latent space (by the selected circuit encoder).

Second, run `python bo_search.py  -save-appendix _cktgnn --model CktGNN  --search-method bo` to search the optimal circuit in Ckt-Bench-301. Expctation improvement searching is used, and users can use `--search-method` to choose the Byesian optimization model (bo = Bohamiann, dngo = DNGO).

Please leave an issue if you have any trouble running the code or suggestions for improvements.