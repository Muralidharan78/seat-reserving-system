# seat reservation system 
import qrcode

from PIL import Image

from pyzbar.pyzbar import decode

import sqlite3

# Function to generate QR code

def generate_qr_code(data, file_name):

    qr = qrcode.QRCode(

        version=1,

        error_correction=qrcode.constants.ERROR_CORRECT_L,

        box_size=10,

        border=4,

    )

    qr.add_data(data)

    qr.make(fit=True)

    img = qr.make_image(fill_color="black", back_color="white")

    img.save(file_name)

# Function to scan QR code

def scan_qr_code(file_name):

    img = Image.open(file_name)

    qr_data = decode(img)

    if qr_data:

        return qr_data[0].data.decode("utf-8")

    else:

        return None

# Function to check booking status

def check_booking_status(booking_id):

    conn = sqlite3.connect("booking.db")

    c = conn.cursor()

    c.execute("SELECT * FROM bookings WHERE booking_id = ?", (booking_id,))

    booking = c.fetchone()

    conn.close()

    return booking

# Main function

def main():

    # Generate QR code for login

    user_id = input("Enter user ID: ")

    generate_qr_code(user_id, "user_qr_code.png")

    print("QR code generated for user ID: ", user_id)

    

    while True:

        # Scan QR code for checking booking status

        print("Scan QR code to check booking status (or type 'exit' to quit): ")

        file_name = input("Enter QR code file name: ")

        if file_name == 'exit':

            break

        booking_id = scan_qr_code(file_name)

        if booking_id:

            booking = check_booking_status(booking_id)

            if booking:

                print("Booking found!")

                print("Booking ID: ", booking[0])

                print("User ID: ", booking[1])

                print("Seat Number: ", booking[2])

            else:

                print("Booking not found. Please try again.")

        else:

            print("Invalid QR code. Please try again.")

    print("Thank you for using the Train Seat Reservation System!")

if __name__ == "__main__":

    main()


