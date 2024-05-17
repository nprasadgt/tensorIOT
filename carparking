import random
import json
import string
import boto3

class Car:
    def __init__(self, license_plate):
        self.license_plate = license_plate

    def __str__(self):
        return self.license_plate

    def park(self, parking_lot, spot):
        if parking_lot[spot] is None:
            parking_lot[spot] = self
            print(f"Car with license plate {self.license_plate} parked successfully in spot {spot}.")
            return True
        else:
            print(f"Spot {spot} is occupied. Trying another spot for car with license plate {self.license_plate}.")
            return False

class ParkingLot:
    def __init__(self, square_footage, spot_size=(8, 12)):
        self.spot_size = spot_size
        self.spot_area = spot_size[0] * spot_size[1]
        self.total_spots = square_footage // self.spot_area
        self.parking_lot = [None] * self.total_spots

    def park_car(self, car):
        empty_spots = [i for i, spot in enumerate(self.parking_lot) if spot is None]
        if not empty_spots:
            print("Parking lot is full.")
            return

        spot = random.choice(empty_spots)
        while not car.park(self.parking_lot, spot):
            empty_spots.remove(spot)
            if not empty_spots:
                print("Parking lot is full.")
                return
            spot = random.choice(empty_spots)

    def map_parked_cars(self):
        parking_map = {}
        for i, spot in enumerate(self.parking_lot):
            if spot:
                parking_map[i] = str(spot)
        return parking_map

    def save_to_json(self, filename):
        parking_map = self.map_parked_cars()
        with open(filename, 'w') as f:
            json.dump(parking_map, f)

    def upload_to_s3(self, filename, bucket_name):
        s3 = boto3.client('s3')
        with open(filename, 'rb') as f:
            s3.upload_fileobj(f, bucket_name, filename)

def generate_license_plate():
    return ''.join(random.choices(string.ascii_uppercase + string.digits, k=7))

def main():
    parking_lot_size = 2000  # square footage
    num_cars = 20
    cars = [Car(generate_license_plate()) for _ in range(num_cars)]
    parking_lot = ParkingLot(parking_lot_size)

    for car in cars:
        parking_lot.park_car(car)

    parking_lot.save_to_json('parking_map.json')
    parking_lot.upload_to_s3('parking_map.json', 'your_bucket_name')

if __name__ == "__main__":
    main()
