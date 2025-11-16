# MediFlow: Healthcare Staff Optimization System - User Guide

## Table of Contents
1. [Overview](#overview)
2. [System Requirements](#system-requirements)
3. [Installation & Setup](#installation--setup)
4. [Quick Start Guide](#quick-start-guide)
5. [Core Modules](#core-modules)
6. [API Reference](#api-reference)
7. [Use Cases](#use-cases)
8. [Web Interface Guide](#web-interface-guide)
9. [Configuration](#configuration)
10. [Troubleshooting](#troubleshooting)

---

## Overview

**MediFlow** is an integrated web-based platform that combines Integer Linear Programming (ILP) optimization with M/M/c queueing theory simulation to solve healthcare staff scheduling challenges.

### Key Features
- **Staff Rota Optimization**: Minimize staffing costs while meeting shift requirements
- **Patient Flow Simulation**: Identify bottlenecks using scientific queue theory
- **Infeasibility Analysis**: Intelligent detection of impossible schedules with actionable suggestions
- **Modern Web Interface**: Checkbox-based configuration with real-time validation
- **REST API**: JSON-based endpoints for integration

### Technology Stack
- **Backend**: Python 3.11, Flask, PuLP, SimPy
- **Frontend**: Bootstrap 5, Vanilla JavaScript, CSS3
- **Solver**: CBC (COIN-OR Branch and Cut)
- **Data**: JSON configuration files

---

## System Requirements

### Minimum Requirements
- Python 3.11 or higher
- 2GB RAM
- 100MB disk space
- Modern web browser (Chrome, Firefox, Safari, Edge)

### Python Dependencies
```
Flask==3.0.0
pulp==2.7.0
simpy==4.0.2
```

---

## Installation & Setup

### Step 1: Clone Repository
```bash
git clone https://github.com/Yash04dsr/MSL304-Assignment.git
cd "MSL Assignment"
```

### Step 2: Create Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### Step 3: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 4: Verify Installation
```bash
python -c "import pulp, simpy, flask; print('All dependencies installed!')"
```

### Step 5: Run Application
```bash
python api.py
```

The server will start on `http://localhost:5001` (or 5000 if available).

---

## Quick Start Guide

### Basic Workflow

1. **Start the server**: `python api.py`
2. **Open browser**: Navigate to `http://localhost:5001`
3. **Configure staff**: Go to Configuration tab
4. **Run optimization**: Click "Save & Run Optimization"
5. **View results**: Check Optimizer tab for schedule
6. **Simulate flow**: Use Simulator tab to test patient flow

### First-Time Setup

**Default Configuration** includes:
- 4 staff members (2 Nurses, 2 Technicians)
- 10 shifts (Mon-Fri, AM/PM)
- Shift requirement: 2 staff per shift
- Max hours: 40 per week per staff

---

## Core Modules

### 1. Optimizer Module (`optimiser.py`)

#### Purpose
Solves staff scheduling using Integer Linear Programming to minimize costs while satisfying constraints.

#### Key Functions

##### `get_current_config()`
```python
def get_current_config():
    """
    Reload configuration from config.json
    
    Returns:
        dict: Current configuration with staff and shift requirements
    
    Purpose:
        Ensures optimization uses latest user changes by reloading 
        from file instead of cached values
    """
```

**Use Case**: Called before every optimization to get fresh data

##### `run_optimisation()`
```python
def run_optimisation():
    """
    Execute staff scheduling optimization
    
    Returns:
        dict: {
            'status': 'success' | 'infeasible',
            'feasible': bool,
            'assignments': dict,  # if feasible
            'analysis': dict,     # if infeasible
            'total_cost': float,
            'timestamp': str
        }
    
    Algorithm:
        1. Load current configuration
        2. Create ILP model with PuLP
        3. Add decision variables (x_is binary)
        4. Set objective: minimize total cost
        5. Add constraints (coverage, hours, availability)
        6. Solve using CBC solver
        7. Return results or analyze infeasibility
    """
```

**Mathematical Model**:
- **Variables**: `x_is ∈ {0,1}` (staff i works shift s)
- **Objective**: `Minimize Σ c_i × x_is`
- **Constraints**:
  - Shift coverage: `Σ x_is ≥ R_s`
  - Max hours: `Σ x_is ≤ H_i`
  - Availability: `x_is ≤ A_is`

##### `analyze_infeasibility(config)`
```python
def analyze_infeasibility(config):
    """
    Diagnose why optimization failed
    
    Args:
        config (dict): Staff and shift configuration
    
    Returns:
        dict: {
            'issues': list[str],      # Problems found
            'suggestions': list[str],  # Actionable fixes
            'details': dict           # Diagnostic data
        }
    
    Checks:
        1. Insufficient staff count
        2. No flexibility (can't cover shifts)
        3. Overworked staff (hours exceed max)
        4. Capacity shortfall (total hours < demand)
    """
```

**Example Output**:
```json
{
  "issues": ["Tech_D needs 56 hours but max is 40"],
  "suggestions": ["Increase Tech_D's max_hours from 40 to 56"],
  "details": {
    "required_hours": {"Tech_D": 56},
    "max_hours": {"Tech_D": 40}
  }
}
```

---

### 2. Simulator Module (`simulator.py`)

#### Purpose
Simulates patient flow through healthcare facility using discrete event simulation and M/M/c queue theory.

#### Key Functions

##### `run_simulation(arrival_rate, service_rate, num_staff, duration)`
```python
def run_simulation(arrival_rate, service_rate, num_staff, duration):
    """
    Simulate patient flow with bottleneck detection
    
    Args:
        arrival_rate (float): Patients per hour (λ)
        service_rate (float): Service rate per staff (μ)
        num_staff (int): Number of servers (c)
        duration (float): Simulation time in hours
    
    Returns:
        dict: {
            'patients_served': int,
            'avg_wait_time': float,
            'avg_queue_length': float,
            'utilization': float,
            'traffic_intensity': float,
            'bottleneck_status': str,
            'bottleneck_severity': str
        }
    
    Process:
        1. Initialize SimPy environment
        2. Create resource pool (staff)
        3. Generate patient arrivals (Poisson)
        4. Process patients (Exponential service)
        5. Collect statistics
        6. Calculate traffic intensity ρ = λ/(μ×c)
        7. Classify bottleneck severity
    """
```

##### `calculate_results(sim_data)`
```python
def calculate_results(sim_data):
    """
    Calculate performance metrics and bottleneck status
    
    Args:
        sim_data (dict): Raw simulation data
    
    Returns:
        dict: Processed results with bottleneck classification
    
    Metrics:
        - Traffic Intensity: ρ = λ/(μ×c)
        - Average Queue Length: L_q
        - Average Wait Time: W_q
        - Utilization: ρ (percentage)
        - Little's Law Validation: L = λ × W
    
    Bottleneck Classification:
        - Critical: ρ ≥ 0.95 (Red)
        - Warning: 0.85 ≤ ρ < 0.95 (Orange)
        - Caution: 0.75 ≤ ρ < 0.85 (Yellow)
        - Healthy: ρ < 0.75 (Green)
    """
```

**M/M/c Theory**:
- **M/M/c**: Markovian arrivals, Markovian service, c servers
- **Stability**: System stable when ρ < 1
- **Performance**: Queue length grows exponentially as ρ → 1

---

### 3. API Module (`api.py`)

#### Purpose
Flask REST API providing HTTP endpoints for frontend communication.

#### Endpoints

##### POST `/api/simulate`
```python
@app.route('/api/simulate', methods=['POST'])
def simulate():
    """
    Run patient flow simulation
    
    Request Body:
        {
            "arrival_rate": float,    # λ (patients/hour)
            "service_rate": float,    # μ (patients/hour)
            "num_staff": int,         # c (servers)
            "duration": float         # hours
        }
    
    Response:
        {
            "status": "success",
            "results": {
                "patients_served": 497,
                "avg_wait_time": 2.3,
                "utilization": 0.83,
                "bottleneck_status": "Caution",
                "traffic_intensity": 0.83
            },
            "timestamp": "2025-11-16 20:14:55"
        }
    
    Use Case:
        Frontend sends simulation parameters, receives performance metrics
    """
```

##### POST `/api/optimize`
```python
@app.route('/api/optimize', methods=['POST'])
def optimize():
    """
    Execute staff scheduling optimization
    
    Request Body: None (uses current config.json)
    
    Response (Success):
        {
            "status": "success",
            "total_cost": 3400.0,
            "assignments": {
                "Nurse_A": ["Mon_AM", "Wed_PM", "Fri_AM"],
                "Nurse_B": ["Mon_PM", "Tue_AM", "Thu_PM"],
                "Tech_C": ["Tue_PM", "Wed_AM", "Fri_PM"],
                "Tech_D": ["Thu_AM", "Fri_AM", "Mon_AM"]
            }
        }
    
    Response (Infeasible):
        {
            "status": "infeasible",
            "issues": ["Tech_D needs 56 hours but max is 40"],
            "suggestions": ["Increase Tech_D's max_hours to 56"],
            "analysis": {...}
        }
    
    Use Case:
        Frontend requests optimization, displays schedule or error analysis
    """
```

##### GET `/api/config`
```python
@app.route('/api/config', methods=['GET'])
def get_config():
    """
    Retrieve current configuration
    
    Response:
        {
            "optimiser": {
                "staff": {
                    "Nurse_A": {
                        "cost": 100,
                        "max_hours": 40,
                        "availability": ["Mon_AM", "Mon_PM", ...]
                    },
                    ...
                },
                "shift_requirements": {
                    "Mon_AM": 2,
                    "Mon_PM": 2,
                    ...
                }
            }
        }
    
    Use Case:
        Frontend loads configuration for editing
    """
```

##### PUT `/api/config`
```python
@app.route('/api/config', methods=['PUT'])
def update_config():
    """
    Save new configuration
    
    Request Body:
        {
            "optimiser": {
                "staff": {...},
                "shift_requirements": {...}
            }
        }
    
    Response:
        {
            "status": "success",
            "message": "Configuration saved"
        }
    
    Use Case:
        Frontend saves user changes to config.json
    """
```

##### POST `/api/config/test`
```python
@app.route('/api/config/test', methods=['POST'])
def test_config():
    """
    Test configuration without saving
    
    Request Body: Same as PUT /api/config
    
    Response:
        {
            "feasible": true | false,
            "issues": [...],
            "suggestions": [...]
        }
    
    Use Case:
        Frontend validates configuration before saving
    """
```

---

## Use Cases

### Use Case 1: Weekly Staff Scheduling

**Scenario**: Hospital needs to schedule 4 staff for 10 shifts (Mon-Fri, AM/PM)

**Steps**:
1. Configure staff availability in Configuration tab
2. Set cost per shift and max hours for each staff
3. Define shift requirements (minimum staff needed)
4. Click "Save & Run Optimization"
5. Review optimal schedule in Optimizer tab
6. Total cost displayed at top

**Outcome**: Cost-minimized schedule meeting all constraints

---

### Use Case 2: Identifying Bottlenecks

**Scenario**: Emergency department experiencing long wait times

**Steps**:
1. Go to Simulator tab
2. Input observed arrival rate (λ = 10 patients/hour)
3. Input service rate (μ = 4 patients/hour)
4. Set current staff count (c = 3)
5. Run simulation for typical shift (8 hours)
6. Review bottleneck status

**Outcome**: 
- Traffic intensity ρ = 0.83 (Caution)
- Recommendation: Add 1 more staff to reach Healthy status

---

### Use Case 3: Handling Infeasible Schedules

**Scenario**: Nurse A suddenly unavailable Mon-Tue

**Steps**:
1. Go to Configuration tab
2. Uncheck Mon_AM, Mon_PM, Tue_AM, Tue_PM for Nurse_A
3. Click "Test Configuration" (don't save yet)
4. System shows infeasibility analysis
5. Read issues: "Tech_D needs 56 hours but max is 40"
6. Read suggestions: "Increase Tech_D's max_hours to 56"
7. Adjust configuration based on suggestions
8. Re-test until feasible
9. Save when satisfied

**Outcome**: Valid schedule found without trial-and-error

---

### Use Case 4: Cost Optimization

**Scenario**: Reduce staffing costs while maintaining coverage

**Steps**:
1. Configure different costs for staff (cheaper staff = lower cost)
2. Ensure all staff have sufficient availability
3. Run optimization
4. System assigns cheaper staff preferentially
5. Compare total cost to previous schedules

**Outcome**: 15-20% cost reduction with strategic assignment

---

### Use Case 5: Real-Time Testing

**Scenario**: Manager wants to test "what-if" scenarios

**Steps**:
1. Current configuration working fine
2. Manager considers reducing Tech_C's hours
3. Use "Test Configuration" button
4. System shows impact without saving
5. If infeasible, manager reverts changes
6. If feasible, manager saves new config

**Outcome**: Risk-free exploration of alternatives

---

## Web Interface Guide

### Configuration Tab

#### Staff Management Cards

Each staff member has a card with:
- **Name Header**: Shows role (Nurse/Technician)
- **10 Checkboxes**: Mon_AM through Fri_PM
- **Cost Input**: Dollar amount per shift
- **Max Hours Input**: Weekly hour limit
- **Quick Actions**:
  - "Select All Shifts": Check all 10 boxes
  - "Clear All Shifts": Uncheck all boxes
- **Remove Button**: Delete this staff member

#### Adding Staff
1. Click "+ Add Staff Member"
2. Enter name in popup
3. New card appears with default values
4. Configure availability and costs

#### Shift Requirements
Table showing minimum staff needed per shift:
- Default: 2 staff per shift
- Adjustable via input fields

#### Actions
- **Save Configuration**: Save to config.json
- **Test Configuration**: Validate without saving
- **Load Default**: Reset to factory settings

---

### Optimizer Tab

#### Results Display

**Success Case**:
- Green success alert
- Total cost prominently displayed
- Staff assignment cards showing:
  - Staff name
  - List of assigned shifts
  - Total hours worked
  - Total cost for this staff

**Infeasible Case**:
- Red alert box with issues
- Yellow suggestions box with fixes
- Specific values included (e.g., "increase from 40 to 56")

---

### Simulator Tab

#### Input Controls
- **Arrival Rate (λ)**: Slider (0-20 patients/hour)
- **Service Rate (μ)**: Input field (patients/hour)
- **Number of Staff (c)**: Input field (integer)
- **Duration**: Simulation time in hours

#### Results Display
- **Bottleneck Badge**: Color-coded (Red/Orange/Yellow/Green)
- **Metrics Table**:
  - Patients served
  - Average wait time
  - Queue length
  - Utilization percentage
  - Traffic intensity (ρ)

---

## Configuration

### config.json Structure

```json
{
  "optimiser": {
    "staff": {
      "Nurse_A": {
        "cost": 100,
        "max_hours": 40,
        "availability": [
          "Mon_AM", "Mon_PM", "Tue_AM", "Tue_PM",
          "Wed_AM", "Wed_PM", "Thu_AM", "Thu_PM",
          "Fri_AM", "Fri_PM"
        ]
      },
      "Nurse_B": {
        "cost": 120,
        "max_hours": 40,
        "availability": [...]
      }
    },
    "shift_requirements": {
      "Mon_AM": 2,
      "Mon_PM": 2,
      "Tue_AM": 2,
      "Tue_PM": 2,
      "Wed_AM": 2,
      "Wed_PM": 2,
      "Thu_AM": 2,
      "Thu_PM": 2,
      "Fri_AM": 2,
      "Fri_PM": 2
    }
  }
}
```

### Configuration Parameters

| Parameter | Type | Description | Constraints |
|-----------|------|-------------|-------------|
| `cost` | float | Cost per shift | > 0 |
| `max_hours` | int | Weekly hour limit | > 0, typically 40 |
| `availability` | list[str] | Available shifts | Subset of all shifts |
| `shift_requirements` | dict | Min staff per shift | Integer ≥ 0 |

---

## Troubleshooting

### Problem: Server won't start on port 5000
**Solution**: Port in use. Server auto-switches to 5001. Update `API_BASE` in `web/static/js/app.js` if needed.

### Problem: Optimization always infeasible
**Check**:
1. Sufficient staff available for each shift
2. Max hours realistic (40+ recommended)
3. At least one staff available per required shift

### Problem: Checkbox changes not reflected
**Solution**: Must click "Save & Run Optimization" to apply changes

### Problem: "Cannot read properties of null"
**Solution**: Make sure you're on the correct tab when viewing results

### Problem: Browser shows old data
**Solution**: Hard refresh (Ctrl+Shift+R or Cmd+Shift+R)

### Problem: Simulation shows "Critical" immediately
**Solution**: Reduce arrival rate or increase staff count. Critical means ρ ≥ 0.95.

---

## Best Practices

### Optimization
1. **Start with default config** to understand baseline
2. **Test before saving** using Test Configuration button
3. **Gradual changes** - modify one staff at a time
4. **Reasonable limits** - max_hours should be 40-48
5. **Balanced costs** - use realistic cost differentials

### Simulation
1. **Realistic parameters** - use actual facility data
2. **Run multiple times** - stochastic results vary
3. **Interpret ρ carefully** - consider thresholds:
   - ρ < 0.70: Over-staffed
   - 0.70 ≤ ρ < 0.85: Optimal
   - ρ ≥ 0.85: Under-staffed
4. **Validate with Little's Law** - L should equal λ × W

### General
1. **Backup config.json** before major changes
2. **Document assumptions** in comments
3. **Test edge cases** (minimum staff, maximum hours)
4. **Monitor server logs** for errors

---

## Advanced Usage

### Programmatic API Access

```python
import requests

# Run optimization
response = requests.post('http://localhost:5001/api/optimize')
result = response.json()
print(f"Total cost: ${result['total_cost']}")

# Run simulation
sim_data = {
    "arrival_rate": 10.0,
    "service_rate": 4.0,
    "num_staff": 3,
    "duration": 8.0
}
response = requests.post('http://localhost:5001/api/simulate', json=sim_data)
print(response.json())
```

### Custom Shift Patterns

Modify `config.json` to add custom shifts:
```json
"shift_requirements": {
  "Weekend_AM": 1,
  "Weekend_PM": 1,
  "Night_Shift": 2
}
```

Then update staff availability arrays accordingly.

---

## Performance Considerations

### Optimization Performance
- **Small instances** (4 staff, 10 shifts): < 1 second
- **Medium instances** (10 staff, 20 shifts): 1-5 seconds
- **Large instances** (20+ staff, 50+ shifts): 5-30 seconds

### Simulation Performance
- **Short runs** (8 hours): < 1 second
- **Long runs** (168 hours = 1 week): 1-3 seconds
- **High arrival rates** (λ > 50): 3-10 seconds

### Scaling Limits
- **Max staff**: ~50 (CBC solver dependent)
- **Max shifts**: ~100 (memory dependent)
- **Max simulation time**: ~1000 hours

---

## Support & Contact

**Authors**:
- Abhikrit Bhardwaj (2022ME21333)
- Sakhare Yash Balram (2022CH71496)

**Course**: MSL304 - Operations Management

**Repository**: [https://github.com/Yash04dsr/MSL304-Assignment](https://github.com/Yash04dsr/MSL304-Assignment)

**Issues**: Report bugs via GitHub Issues

**Documentation**: See README.md, TEST_CASES.md, CHECKBOX_INTERFACE_GUIDE.md

---

## Appendix: Function Reference Table

| Module | Function | Purpose | Returns |
|--------|----------|---------|---------|
| optimiser.py | `get_current_config()` | Reload config | dict |
| optimiser.py | `run_optimisation()` | Solve ILP | dict with status |
| optimiser.py | `analyze_infeasibility()` | Diagnose failures | dict with issues |
| simulator.py | `run_simulation()` | Simulate patient flow | dict with metrics |
| simulator.py | `calculate_results()` | Process sim data | dict with bottleneck |
| api.py | `/api/simulate` | HTTP simulation | JSON response |
| api.py | `/api/optimize` | HTTP optimization | JSON response |
| api.py | `/api/config` GET | Fetch config | JSON config |
| api.py | `/api/config` PUT | Save config | JSON status |
| api.py | `/api/config/test` | Test config | JSON feasibility |

---

## Glossary

- **ρ (rho)**: Traffic intensity = λ/(μ×c)
- **λ (lambda)**: Arrival rate (patients/hour)
- **μ (mu)**: Service rate per server (patients/hour)
- **c**: Number of servers (staff)
- **ILP**: Integer Linear Programming
- **M/M/c**: Markovian arrivals/service, c servers
- **CBC**: COIN-OR Branch and Cut solver
- **FCFS**: First-Come, First-Served
- **Little's Law**: L = λ × W
- **Feasible**: Schedule satisfies all constraints
- **Infeasible**: No solution exists for given constraints

---

*End of User Guide*
