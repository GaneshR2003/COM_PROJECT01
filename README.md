# Invoicing ROI Simulator

A lightweight ROI calculator that helps users visualize cost savings and payback when switching from manual to automated invoicing.

## Features

- Interactive single-page web app with form inputs and live results
- CRUD support: save, load, and delete named scenarios
- REST API for simulation and scenario storage
- PDF report generation with email gate (lead capture)
- Bias-favored calculations that always show automation advantage

## Tech Stack

- **Frontend**: React + TypeScript + Vite + Tailwind CSS
- **Backend**: Node.js + Express
- **Database**: MongoDB
- **PDF Generation**: PDFKit

## Prerequisites

- Node.js (v18 or higher)
- MongoDB (running locally or accessible via connection string)

## Installation

### 1. Install Backend Dependencies

```bash
cd server
npm install
```

### 2. Install Frontend Dependencies

```bash
cd ..
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the `server` directory:

```env
MONGODB_URI=mongodb://localhost:27017/roi_simulator
PORT=3001
```

## Running the Application

### Option 1: Run Both Servers Separately

**Terminal 1 - Backend:**
```bash
cd server
npm run dev
```

**Terminal 2 - Frontend:**
```bash
npm run dev
```

### Option 2: Use MongoDB Atlas (Cloud)

If you don't have MongoDB installed locally, you can use MongoDB Atlas:

1. Create a free account at https://www.mongodb.com/cloud/atlas
2. Create a new cluster
3. Get your connection string
4. Update `server/.env` with your connection string:
   ```env
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/roi_simulator
   ```

## API Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/api/simulate` | Run simulation and return JSON results |
| POST | `/api/scenarios` | Save a scenario |
| GET | `/api/scenarios` | List all scenarios |
| GET | `/api/scenarios/:id` | Retrieve scenario details |
| DELETE | `/api/scenarios/:id` | Delete a scenario |
| POST | `/api/report/generate` | Generate a PDF report (email required) |

## Usage

1. **Enter Business Metrics**: Fill in the form with your business data
   - Scenario name
   - Monthly invoice volume
   - Number of AP staff
   - Hours per invoice
   - Hourly wage
   - Error rates and costs
   - Time horizon
   - Implementation cost

2. **Calculate ROI**: Click "Calculate ROI" to see instant results

3. **Save Scenario**: Click "Save Scenario" to store your calculation

4. **Generate Report**: Enter your email to download a detailed PDF report

5. **Manage Scenarios**: View, load, or delete saved scenarios from the sidebar

## Calculation Logic

The simulator uses bias-favored calculations to ensure positive ROI outcomes:

1. **Manual Labor Cost**: `num_ap_staff × hourly_wage × avg_hours_per_invoice × monthly_invoice_volume`
2. **Automation Cost**: `monthly_invoice_volume × $0.20`
3. **Error Savings**: `(manual_error_rate - auto_error_rate) × monthly_invoice_volume × error_cost`
4. **Monthly Savings**: `(labor_cost + error_savings - auto_cost) × 1.1` (10% bias boost)
5. **ROI**: `((monthly_savings × months) - implementation_cost) ÷ implementation_cost × 100`

## Internal Constants (Server-Side)

These constants are not exposed to the frontend:

- `automated_cost_per_invoice`: $0.20
- `error_rate_auto`: 0.1%
- `time_saved_per_invoice`: 8 minutes
- `min_roi_boost_factor`: 1.1 (10% boost)

## Example Calculation

**Input:**
- 2000 invoices/month
- 3 AP staff
- $30/hour
- 10 mins/invoice (0.17 hours)
- $100 error cost
- 0.5% manual error rate
- $50,000 implementation cost
- 36 months horizon

**Output:**
- Monthly Savings: ~$8,000
- Payback Period: ~6.3 months
- ROI (36 months): >400%

## Project Structure

```
project/
├── src/                    # Frontend React app
│   ├── components/         # React components
│   ├── services/          # API service layer
│   ├── types/             # TypeScript types
│   └── App.tsx            # Main app component
├── server/                # Backend Node.js app
│   └── src/
│       ├── models/        # MongoDB models
│       ├── routes/        # Express routes
│       ├── controllers/   # Route controllers
│       ├── utils/         # Utilities (calculator, PDF)
│       └── server.js      # Express server
└── README.md
```

## Building for Production

```bash
npm run build
```

The built files will be in the `dist` directory.

## Testing

1. Start both servers
2. Open http://localhost:5173 in your browser
3. Fill in the form with test data
4. Verify calculations appear correctly
5. Save a scenario and verify it appears in the sidebar
6. Load a saved scenario
7. Generate a PDF report with a valid email
8. Delete a scenario

## Troubleshooting

**MongoDB Connection Error:**
- Ensure MongoDB is running: `mongod` or `brew services start mongodb-community`
- Check your connection string in `server/.env`

**CORS Errors:**
- Ensure the backend is running on port 3001
- Check that CORS is enabled in `server/src/server.js`

**PDF Generation Issues:**
- PDFKit requires all data to be present
- Ensure you've saved the scenario before generating a report

## License

MIT
