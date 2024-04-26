### OpsML

>  https://thorrester.github.io/opsml-ghpages/

Cards: Track, version and store a variety of ML artifacts via cards (data, models, runs, projects) and a SQL-based card registry system.

Supports streaming (can use big data and models!).

Supports a variety of storage backends (local, gcs, s3).

#### Data Libraries

| Name   | Opsml Implementation |
| ------ | :------------------: |
| Pandas |     `PandasData`     |
| Polars |     `PolarsData`     |
| Torch  |     `TorchData`      |
| Arrow  |     `ArrowData`      |
| Numpy  |     `NumpyData`      |
| Sql    |      `SqlData`       |
| Text   |    `TextDataset`     |
| Image  |    `ImageDataset`    |

#### Model Libraries

| Name            | Opsml Implementation |                           Example                            |
| --------------- | :------------------: | :----------------------------------------------------------: |
| Sklearn         |    `SklearnModel`    | [link](https://github.com/shipt/opsml/blob/main/examples/sklearn/basic.py) |
| LightGBM        |   `LightGBMModel`    | [link](https://github.com/shipt/opsml/blob/main/examples/boosters/lightgbm_boost.py) |
| XGBoost         |    `XGBoostModel`    | [link](https://github.com/shipt/opsml/blob/main/examples/boosters/xgboost_sklearn.py) |
| CatBoost        |   `CatBoostModel`    | [link](https://github.com/shipt/opsml/blob/main/examples/boosters/catboost_example.py) |
| Torch           |     `TorchModel`     | [link](https://github.com/shipt/opsml/blob/main/examples/torch/torch_example.py) |
| Torch Lightning |   `LightningModel`   | [link](https://github.com/shipt/opsml/blob/main/examples/torch/torch_lightning_example.py) |
| TensorFlow      |  `TensorFlowModel`   | [link](https://github.com/shipt/opsml/blob/main/examples/tensorflow/tf_example.py) |
| HuggingFace     |  `HuggingFaceModel`  | [link](https://github.com/shipt/opsml/blob/main/examples/huggingface/hf_example.py) |
| VowpalWabbit    | `VowpalWabbitModel`` | [link](https://github.com/shipt/opsml/blob/main/examples/vowpal/vowpal_example.py) |

`Opsml` requires:

- **OPSML_TRACKING_URI**: This is the sql tracking uri to your card registry database.  If this variable is not set, it will default to a local `SQLite` connection.
- **OPSML_STORAGE_URI**: This is the storage uri to use for storing ml artifacts (models, data, figures, etc.). If interacting with an `Opsml` server, this variable does not need to be set.

#### Interface Types

- [`DataInterface`](https://thorrester.github.io/opsml-ghpages/interfaces/data/interfaces/): Interface used to store data-related information (data, dependent variables, feature descriptions, split logic, etc.)
- [`ModelInterface`](https://thorrester.github.io/opsml-ghpages/interfaces/model/interfaces/): Interface used to store trained model and model information

#### Deployment

![image-20240408103324626](/Users/anton/MyDocuments/Notes/machine-learning/.opsml-images/image-20240408103324626.png)

#### Cards

![image-20240408103441381](/Users/anton/MyDocuments/Notes/machine-learning/.opsml-images/image-20240408103441381.png)

- `DataCard`: Card used to store data-related information (``, dependent variables, feature descriptions, split logic, etc.)
- `ModelCard`: Card used to store trained model and model information
- `RunCard`: Stores artifact and metric info related to Data, Model, or Pipeline cards.
- `ProjectCard`: Stores information related to unique projects. You will most likely never interact with this card directly.

All `ArtifactCards` follow a Semver version format (`major.minor.patch`). By default, a `minor` increment is used whenever a card is registered. If a version is provided, it overrides the default version type.

`AuditCards` serve a special purpose in `OpsML`  and can be viewed as a card that contains governance, compliance, and  ethical information about a particular model and its associated assets. Importantly, information contained in an `AuditCard` can be used be governing bodies and stakeholders to assess and approve a model for production.

Out of the box, `Opsml` comes pre-installed with a `Opsml-Cli` that can be used as a CLI to interact with an `Opsml` server.

#### How OpsML Works

![image-20240408134143854](/Users/anton/MyDocuments/Notes/machine-learning/.opsml-images/image-20240408134143854.png)

![image-20240408135512256](/Users/anton/MyDocuments/Notes/machine-learning/.opsml-images/image-20240408135512256.png)

#### Ownership

![image-20240408135905645](/Users/anton/MyDocuments/Notes/machine-learning/.opsml-images/image-20240408135905645.png)