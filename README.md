# RTE Tempo API Integration for Node-RED

![image](https://github.com/user-attachments/assets/54a2c938-aa88-40bc-b461-c3392dd14625)

This Node-RED flow provides integration with RTE's (Réseau de Transport d'Électricité) Tempo API to retrieve and manage Tempo color day information. The flow handles OAuth2 authentication, periodic data retrieval, and maintains a record of Tempo color days.

## Features

- OAuth2 authentication management with automatic token refresh
- Daily retrieval of Tempo color information
- Tracking of used and remaining Tempo days by color (Blue, White, Red)
- Storage of current and next day's Tempo colors in global context
- Automatic error handling and status reporting

## Prerequisites

- Node-RED installation
- RTE API credentials (Client ID and Secret)
- Required Node-RED nodes:
  - node-red-contrib-oauth2
  - node-red-node-function

## Configuration

### RTE API Registration

Before using this flow, you need to register and obtain API credentials from RTE:

1. Visit the [RTE API Portal](https://data.rte-france.com/catalog/-/api/consumption/Tempo-Like-Supply-Contract/v1.1)
2. Create an account and subscribe to the Tempo API
3. Generate your Client ID and Secret ID

### Environment Variables

The flow requires two environment variables that need to be set in the flow properties:

- `RTE_CLIENT_ID`: Your RTE API client ID
- `RTE_SECRET_ID`: Your RTE API client secret

To configure these credentials:

1. Double-click on the flow tab or select "Configure" from the flow menu
2. Go to the "Environment Variables" tab
3. Add your credentials as shown below:

![image](https://github.com/user-attachments/assets/4064de2e-ce23-40f5-8577-e14b9395116d)

*Note: Make sure to keep your credentials secure and never share them in your flow exports.*

### Flow Structure

The flow consists of two main groups:

1. **RTE authentication management**
   - Handles OAuth2 token acquisition and refresh
   - Maintains authentication state
   - Automatically refreshes tokens every 60 minutes

2. **Get Tempo color from RTE**
   - Retrieves Tempo calendar data daily at 12:00
   - Processes and stores color information
   - Maintains counts of used and remaining days by color

## Usage

The flow automatically:

1. Initializes OAuth2 authentication on startup
2. Refreshes the authentication token every hour
3. Retrieves Tempo data daily at noon
4. Stores the processed data in the global context as `RTEtempoColor`

### Stored Data Structure

The flow stores data in the following format:

```javascript
{
  "today": {
    "date": "YYYY-MM-DD",
    "color": "Bleu|Blanc|Rouge"
  },
  "tomorrow": {
    "date": "YYYY-MM-DD",
    "color": "Bleu|Blanc|Rouge"
  },
  "days": {
    "passed": {
      "Blue": number,
      "White": number,
      "Red": number
    },
    "remaining": {
      "Blue": number,
      "White": number,
      "Red": number
    }
  }
}
```

### Example Output

Here's an example of the actual output message from the flow:

```javascript
{
  "payload": {
    "today": {
      "date": "2025-01-12",
      "color": "Bleu"
    },
    "tomorrow": {
      "date": "2025-01-13",
      "color": "Rouge"
    },
    "days": {
      "passed": {
        "Blue": 101,
        "White": 21,
        "Red": 12
      },
      "remaining": {
        "Blue": 198,
        "White": 22,
        "Red": 10
      }
    }
  }
}
```

This shows:
- Today's color (Blue) and tomorrow's color (Red)
- Number of days used so far this season (101 Blue, 21 White, 12 Red)
- Number of remaining days (198 Blue, 22 White, 10 Red)

## API Details

The flow interacts with RTE's API endpoints:

- OAuth2 Token Endpoint: `https://digital.iservices.rte-france.com/token/oauth/`
- Tempo Calendar API: `https://digital.iservices.rte-france.com/open_api/tempo_like_supply_contract/v1/tempo_like_calendars`

## Error Handling

The flow includes comprehensive error handling:
- Authentication failures
- Missing credentials

Status information is displayed in the Node-RED UI for each node.

## Installation

1. Import the flow into Node-RED using the provided JSON
2. Configure the environment variables with your RTE API credentials in the flow properties
3. Deploy the flow

## Contributing

Feel free to submit issues and enhancement requests.

## License

This project is released under the MIT License.

## Author

Ikes
