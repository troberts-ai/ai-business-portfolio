# AI Business Portfolio

Selected applied AI, analytics, governance, and data visualization projects demonstrating how I translate domain knowledge in medicine and law into practical decision-support systems.

My background combines oncology, corporate legal training, healthcare operations, governance, and applied AI. This portfolio reflects that intersection: building analytical workflows for high-stakes environments where outputs must be useful, explainable, auditable, and operationally relevant.

The projects include sanitized summaries, screenshots, selected outputs, methodology notes, and visualization files from applied work completed during my Master of Science in Artificial Intelligence in Business at Arizona State University's W. P. Carey School of Business.

This repository is intended as a public portfolio companion to my LinkedIn profile and professional outreach.

## Featured Projects

### 1. ScottsdaleEZ Predictive Model

A municipal predictive analytics capstone for the City of Scottsdale. The model forecasts next-month service request volume by ZIP code and request type, supporting early warning, operational planning, and executive decision-making.

**Methods & tools:** LightGBM, Python, AWS SageMaker monthly batch processing, Athena, S3. WMAPE 0.451 on holdout. Blended-shrinkage fallback for sparse ZIP × request-type cells.

**Focus areas:**
- Predictive analytics for public-sector operations
- Forecasting and early-warning workflows
- Executive-readable methodology and outputs

### 2. ScottsdaleEZ Power BI Dashboard

A Power BI decision-support layer translating model outputs and service-request data into operational views for city staff. Helps users review predicted concerns, service target performance, request completion patterns, and seasonal changes.

**Methods & tools:** Power BI. Five-tab structure — Overview · Hotspots & Predictions · Workgroup + SLA Performance · Resolution Time Distribution · Seasonal Comparison.

**Focus areas:**
- Operational dashboards and forecast interpretation
- Decision support for nontechnical users
- Executive communication

### 3. Selected Tableau Visualization Examples

Selected Tableau workbooks and representative screenshots focused on dashboard design, chart selection, visual storytelling, and business communication.

**Methods & tools:** Tableau Desktop, Tableau Public.

**Focus areas:**
- Tableau dashboard design and chart selection
- Visual storytelling and data-to-decision communication

## Portfolio Principles

- Business use comes first — analytical outputs should support decisions, not create false precision.
- Governance, validation, and explainability matter in regulated environments — a habit carried over from clinical practice and corporate legal work.
- Dashboards and models should be usable by real operators, not only technical teams.
- Public artifacts are sanitized to protect partner/client data, restricted course materials, and proprietary work.

## Repository Structure

```text
ai-business-portfolio/
├── README.md
├── 01_scottsdaleez_predictive_model/
├── 02_scottsdaleez_powerbi_dashboard/
└── 03_tableau_visualization_examples/
