# Galen COVID-19 Automated Diagnostic System

Production machine learning system for automated COVID-19 diagnosis from PCR curve data. Deployed at Gravity Diagnostics during the pandemic.

## Production Impact

- **100% diagnostic accuracy** across 1,000,000+ production cases
- **Zero reported errors** when validated against human lab technicians
- Baseline human lab tech accuracy: ~96%
- Processing time: **15 minutes → <1 second**
- Throughput: **30,000+ tests processed daily**
- Deployment period: October 2020 - January 2021

## Validation Methodology

The system was validated through parallel processing in production:
- Neural network predictions ran alongside human lab technician diagnoses
- Lab director confirmed zero corrections needed from ML predictions during validation phase
- Human technicians corrected their own initial diagnoses ~4% of the time
- ML system required no corrections in over 1 million cases

This wasn't test set accuracy - this was real production deployment with lives depending on correct diagnoses.

## Technical Architecture

### Ensemble Approach

The system uses an ensemble of 5 specialized neural networks, each trained to detect specific COVID-19 genetic markers:

1. **MS2 Control Gene** - Internal control validation
2. **N Gene** - Nucleocapsid protein detection
3. **ORF1ab** - Open reading frame detection
4. **RP (RNase P)** - Human gene control
5. **S Gene** - Spike protein detection

**Why ensemble?**
- Each model specializes in detecting a specific viral or control gene
- Multi-target detection increases diagnostic confidence
- Redundancy across genetic markers reduces false negatives
- Follows standard clinical PCR protocols (multi-gene confirmation)
- Ensemble voting provides robust classification even with degraded samples

Each model outputs a confidence score, and the final diagnosis is determined by aggregating predictions across all five genetic targets - mirroring how lab technicians interpret multi-gene PCR results.

### Model Pipeline

1. **Data Ingestion**: PCR curve data from Quant Studio instruments (Excel format)
2. **Preprocessing**: Curve normalization, feature extraction, data augmentation
3. **Inference**: Ensemble prediction across 5 networks
4. **Validation**: Confidence thresholding and quality checks
5. **Output**: Binary classification (positive/negative) with confidence scores

### Technology Stack

- **Framework**: TensorFlow / Keras for training
- **Deployment**: TensorFlow.js for browser-based inference
- **Training Data**: 175,000+ hand-labeled real-world PCR curves
  - 80/20 train-test split with randomization
  - Stratified sampling to maintain class balance
  - Data collected across diverse sample conditions and collection methods
- **Architecture**: CNN for spatial feature extraction + LSTM for temporal pattern recognition
- **Optimization**: Model quantization and pruning for browser deployment

## Repository Contents

```text
data_files/           # Training/testing PCR curve data (CSV format)
jupyter_notebooks/
├── Data Transformation.ipynb      # Production data preprocessing pipeline
├── Ensemble AI Creation.ipynb     # Model training and ensemble assembly
└── Original Notebook.ipynb        # Initial experimentation and prototyping
models/
├── h5_models/                     # Keras model files (5 gene-specific models)
└── tfjs_models/                   # TensorFlow.js converted models for browser deployment
```

## Development Notebooks

The repository includes Jupyter notebooks documenting the full ML pipeline:

- **Data Transformation**: Preprocessing pipeline for PCR curve normalization, feature extraction, and augmentation
- **Ensemble AI Creation**: Training process for the 5 gene-specific models and ensemble architecture
- **Original Notebook**: Initial experimentation showing the iterative development process (retained for transparency)

These notebooks demonstrate the complete workflow from raw PCR data to deployable TensorFlow.js models.

## Development Context

Built as sole ML engineer over 3-month period with direct feedback from:
- Lab technicians (daily users)
- Lab directors (medical validation)
- Gravity Diagnostics leadership

The tight feedback loop with actual users was critical - we iterated on edge cases they encountered in real-world samples that wouldn't appear in clean training data.

## Data Engineering

One of the project's most significant undertakings was creating a high-quality labeled dataset at scale:

**Dataset Characteristics:**
- **175,000+ labeled PCR curves** from real patient samples
- Hand-labeled by experienced lab technicians
- Covered diverse conditions: varying viral loads, sample quality, collection methods
- Balanced across positive/negative cases and all five genetic markers

**Data Pipeline:**
- 80/20 train-test split with stratified sampling
- Randomization to prevent learning from dataset ordering artifacts
- Validation against independent lab technician diagnoses
- Cross-validation across multiple lab sites and time periods

This dataset scale is what enabled the production reliability - the models were trained on the full complexity of real-world samples, not idealized laboratory conditions.

## Key Technical Challenges Solved

1. **Variability in sample quality**: Different collection methods, storage conditions, viral loads
2. **Real-time inference constraints**: Had to run in browser on lab technician workstations
3. **Zero tolerance for false negatives**: Medical context required extreme reliability
4. **Integration with existing workflow**: Had to fit seamlessly into lab's Excel-based process

## Performance Metrics

| Metric | Value |
|--------|-------|
| Accuracy (Production) | 100% |
| Sensitivity | 100% |
| Specificity | 100% |
| Inference Time | <1 second |
| Daily Throughput | 30,000+ tests |
| False Positives | 0 (validated) |
| False Negatives | 0 (validated) |

## Related Projects

- [Galen-Front-End](link) - React/TypeScript UI for lab technicians

## License

[Your license]

## Acknowledgments

Built in partnership with Gravity Diagnostics during the COVID-19 pandemic. Special thanks to the lab technicians and directors who provided real-world feedback and validation.
