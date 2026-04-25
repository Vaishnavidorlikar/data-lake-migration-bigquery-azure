# Data Lake Migration - Kaggle to BigQuery to Azure

A comprehensive data pipeline for migrating data from Kaggle datasets to Google BigQuery and then to Azure Data Lake Storage. This project provides a robust ETL (Extract, Transform, Load) framework with configurable transformations, data quality checks, and monitoring capabilities.

## Project Structure

```
data-lake-migration-bigquery-azure/
│
├── data/
│   ├── raw/                    # Raw data files
│   │   └── sample_data.csv
│   └── processed/              # Processed data files
│       └── output.csv
│
├── notebooks/
│   └── analysis.ipynb         # Data analysis and exploration
│
├── src/                       # Source code
│   ├── __init__.py
│   ├── extract.py             # BigQuery data extraction
│   ├── transform.py           # Data transformation and cleaning
│   ├── load.py                # Azure data loading
│   └── pipeline.py            # ETL pipeline orchestration
│
├── tests/
│   └── test_pipeline.py       # Unit and integration tests
│
├── config/
│   ├── config.yaml            # Main configuration file
│   ├── looker_studio_config.json  # Looker Studio dashboard config
│   └── azure_workbook_config.json  # Azure workbook config
│
├── docs/
│   └── ARCHITECTURE.md        # Detailed architecture documentation
│
├── requirements.txt            # Python dependencies
├── README.md                  # This file
└── main.py                    # Main application entry point
```

## Features

- **Kaggle Integration**: Download datasets from Kaggle using API authentication
- **BigQuery Loading**: Load Kaggle data directly into BigQuery tables
- **Extract**: Extract data from BigQuery using tables or custom SQL queries
- **Transform**: Comprehensive data cleaning, type conversion, filtering, and aggregation
- **Load**: Support for Azure Blob Storage, Azure Data Lake Storage Gen2, and local file systems
- **Live Pipeline**: Real-time data processing with WebSocket streaming
- **Dashboards**: Interactive dashboards via Google Colab, Looker Studio, and Azure
- **API Server**: RESTful endpoints for dashboard data integration
- **Configuration**: YAML-based configuration for different environments
- **Monitoring**: Built-in logging, email notifications, and Slack integration
- **Data Quality**: Automated data validation and quality checks
- **Testing**: Comprehensive unit and integration tests

## Data Flow Process

1. **Kaggle to BigQuery**: `Kaggle Datasets → Extract → Load to BigQuery`
2. **BigQuery to Azure**: `BigQuery → Extract → Transform → Load to Azure Storage`
3. **Real-time Processing**: `Azure Storage → Live Pipeline → WebSocket/API → Dashboards`
4. **Monitoring**: `Pipeline → Metrics/Logs → Alert System → Notifications`

## Prerequisites

- Python 3.8 or higher
- Google Cloud Platform account with BigQuery access
- Azure subscription with Storage account
- Kaggle account with API credentials
- Service account credentials for both GCP and Azure

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd data-lake-migration-bigquery-azure
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

## Configuration

1. Copy the configuration template:
```bash
cp config/config.yaml config/config.local.yaml
```

2. Update `config/config.local.yaml` with your specific settings:

### BigQuery Configuration
```yaml
bigquery:
  project_id: "your-gcp-project-id"
  credentials_path: "path/to/service-account-key.json"
```

### Azure Configuration
```yaml
azure:
  connection_string: "DefaultEndpointsProtocol=https;AccountName=yourstorageaccount;AccountKey=yourkey;EndpointSuffix=core.windows.net"
  account_name: "yourstorageaccount"
  container: "data-lake-container"
```

### Transformation Configuration
```yaml
transformations:
  clean:
    remove_nulls: true
  data_types:
    id: "int64"
    age: "int64"
  derived_columns:
    salary_age_ratio: "salary / age"
```

## Usage

### Basic Usage

```python
from src.pipeline import Pipeline

# Initialize pipeline with configuration
pipeline = Pipeline(config_path="config/config.yaml")

# Define source, transformation, and target configurations
source_config = {
    "table": {
        "dataset": "your_dataset",
        "name": "your_table"
    }
}

transform_config = {
    "clean": {"remove_nulls": True},
    "data_types": {"age": "int64"}
}

target_config = {
    "azure_datalake": {
        "file_system": "processed-data",
        "file_path": "output/data.csv"
    }
}

# Run the pipeline
success = pipeline.run_pipeline(source_config, transform_config, target_config)
```

### Command Line Usage

```bash
python main.py --config config/config.yaml --source dataset.table --target azure_datalake
```

### Using the Jupyter Notebook

```bash
jupyter notebook notebooks/analysis.ipynb
```

## Testing

Run the test suite:

```bash
# Run all tests
python -m pytest tests/

# Run with coverage
python -m pytest tests/ --cov=src

# Run specific test
python -m pytest tests/test_pipeline.py::TestTransformer
```

## Data Quality Checks

The pipeline includes built-in data quality checks:

- **Row Count Validation**: Ensures minimum/maximum row counts
- **Null Check**: Validates null percentage in critical columns
- **Duplicate Check**: Identifies duplicate records
- **Range Check**: Validates data ranges for numeric columns
- **Custom Checks**: Allows for custom validation expressions

Configure quality checks in `config.yaml`:

```yaml
data_quality:
  checks:
    - type: "row_count"
      min_rows: 1
      max_rows: 1000000
    - type: "null_check"
      columns: ["id", "name"]
      max_null_percentage: 5
```

## Monitoring and Alerting

### Logging
- Configurable log levels (DEBUG, INFO, WARNING, ERROR)
- File-based logging with rotation
- Structured logging for better analysis

### Email Notifications
```yaml
monitoring:
  email:
    enabled: true
    smtp_server: "smtp.gmail.com"
    recipients: ["admin@company.com"]
```

### Slack Integration
```yaml
monitoring:
  slack:
    enabled: true
    webhook_url: "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"
    channel: "#data-pipeline-alerts"
```

## Advanced Features

### Partitioned Data Loading
```python
# Load data partitioned by specific columns
loader.load_partitioned(
    df=df,
    base_path="data/partitioned",
    partition_columns=["year", "month", "day"]
)
```

### Custom Transformations
```python
# Add custom transformation logic
transformer.add_derived_columns({
    "age_group": "pd.cut(age, bins=[0, 30, 50, 100], labels=['Young', 'Adult', 'Senior'])",
    "salary_tier": "np.where(salary > 75000, 'High', 'Standard')"
})
```

### Performance Optimization
- Chunked processing for large datasets
- Parallel processing with configurable workers
- Memory-efficient operations with Dask integration

## Environment Support

The configuration supports multiple environments:

- **Development**: Debug logging, smaller data limits
- **Staging**: Full data processing, testing configurations
- **Production**: Optimized settings, monitoring enabled

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Ensure service account credentials are correctly configured
   - Verify proper IAM permissions in both GCP and Azure

2. **Memory Issues**
   - Reduce chunk size in configuration
   - Enable Dask for distributed processing

3. **Connection Timeouts**
   - Increase timeout values in configuration
   - Check network connectivity to cloud services

### Debug Mode

Enable debug logging for detailed troubleshooting:

```yaml
pipeline:
  logging:
    level: "DEBUG"
```

## 📞 Support

For support and questions:
- Create an issue in the GitHub repository
- Contact the data engineering team
- Check the documentation in the `/docs` directory

## 🔄 Version History

- **v1.0.0**: Initial release with core ETL functionality
- **v1.1.0**: Added data quality checks and monitoring
- **v1.2.0**: Enhanced transformation capabilities
- **v1.3.0**: Added partitioned loading support

---

**Built with ❤️ by the Data Engineering Team**
