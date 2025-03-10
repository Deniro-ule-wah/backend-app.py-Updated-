# backend-app.py-Updated-

from flask import Flask, jsonify, request
from flask_cors import CORS
from models.database import db, Vehicle, Alert
from services.tracking import update_vehicle_position
from services.alerts import check_all_alerts
from config import Config
from utils.validation import validate_vehicle_data

app = Flask(__name__)
app.config.from_object(Config)
CORS(app)
db.init_app(app)

@app.route('/vehicles', methods=['GET'])
def get_vehicles():
    # ... (unchanged from previous version)

@app.route('/update_location', methods=['POST'])
def update_location():
    try:
        data = request.json
        if not validate_vehicle_data(data):
            return jsonify({"error": "Invalid data format"}), 400
        
        vehicle = update_vehicle_position(data)
        check_all_alerts(vehicle)  # Now includes AI prediction
        return jsonify({"status": "success", "vehicle_id": vehicle.vehicle_id})
    except Exception as e:
        return jsonify({"error": str(e)}), 500

# ... (other routes unchanged)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
