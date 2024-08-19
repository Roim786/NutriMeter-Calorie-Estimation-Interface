# NutriMeter-Calorie-Estimation-Interface

A GUI application for estimating food calories using YOLOv8 for object detection and a Tkinter-based interface for calorie display. The tool identifies food items from images, calculates their total caloric value, and presents the results in a user-friendly format.

# After Training YOLOv8 model on custom dataset of food
import cv2

from ultralytics import YOLO

import tkinter as tk

from tkinter import Label

from PIL import Image, ImageTk

# Load the YOLOv8 model

model = YOLO(r'C:\Users\PMLS\Downloads\Epoch30 result\train5-20240814T045607Z-001\train5\weights\best.pt')

# Load the image
#image = cv2.imread(r'C:\Users\PMLS\Downloads\DataFood\test\images\12_jpg.rf.838ad21e52b42b2f17dfafe821710e85.jpg')

#img_resize= cv2.resize(image,(640,640))

# Perform inference

results = model.predict("breadEggsetc.jpg", save=True, imgsz=320, conf=0.5)

detected_image=results[0].plot() # To plot/make bounding boxes along the detected objects of the image

#make detected list to show on console and to compare with the food calorie dict to find the calories

detected_list= [] 

#detections = results[0].boxes ,,,, Accesses the bounding boxes like we did for plotting in the detection result.

for result in results:

    for detection in result.boxes:

        #confidence  = float(detection.conf[0])
        
        ids = int (detection.cls[0])
        
        class_name = result.names[ids] 
        
        detected_list.append(
        
        {
             class_name
             
            #'Confidence' : confidence
        }
    )
    
detected_list = [items.pop() for items in detected_list ]  # changing list of sets into a list

#Food calories dictionary

food_calories = {

    'apple': 104, 'avocado': 320, 'bacon': 936, 'banana': 180, 'beef': 500, 'blueberries': 114, 
    'bread': 560, 'burdock': 60, 'butter': 1440, 'cabbage': 50, 'canned_corn': 180, 'canned_tuna': 220, 
    'carrot': 80, 'cheese': 800, 'chicken': 330, 'chili_powder': 380, 'chocolate': 1040, 'chocolate_bread': 680, 
    'cinnamon': 160, 'cooking_oil': 1800, 'corn': 180, 'cornflake': 780, 'crab_meat': 200, 'cucumber': 32, 
    'curry_powder': 380, 'dumpling': 300, 'egg': 280, 'eggs': 280, 'fish_cake': 180, 'flour': 720, 
    'french_fries': 640, 'garlic': 320, 'ginger': 160, 'goat_cheese': 780, 'green_beans': 70, 
    'green_onion': 80, 'ground_beef': 480, 'ham': 340, 'hash_brown': 440, 'heavy_cream': 1500, 
    'hotdog': 440, 'ice': 0, 'ketchup': 280, 'kimchi': 50, 'kochujang': 340, 'lemon': 60, 
    'lemon_juice': 50, 'lime': 60, 'mandarin': 100, 'marshmallow': 720, 'mayonnaise': 1400, 'milk': 120, 
    'mozzarella cheese': 640, 'mushroom': 40, 'mustard': 120, 'nacho_chips': 1040, 'noodle': 360, 
    'nutella': 1080, 'olive_oil': 1800, 'onion': 80, 'orange': 80, 'oreo': 960, 'parmesan_cheese': 820, 
    'parsley': 50, 'pasta': 360, 'peanut_butter': 1180, 'pear': 104, 'pepper': 80, 'pepper_powder': 380, 
    'pickle': 30, 'pickled_radish': 50, 'pimento': 40, 'pineapple': 100, 'pork': 470, 'potato': 160, 
    'ramen': 340, 'red_wine': 180, 'rice': 260, 'salt': 0, 'sausage': 600, 'seaweed': 50, 
    'sesame': 1200, 'sesame_oil': 1800, 'shrimp': 200, 'shrimp_paste': 220, 'soy_sauce': 60, 
    'spam': 550, 'spinach': 46, 'squid': 200, 'strawberry': 80, 'sugar': 800, 'sweet_potato': 180, 
    'tofu': 160, 'tomato': 40, 'wasabi': 120, 'watermelon': 60, 'whipping_cream': 1200
    
}

#Matching the detected classes from food calories dict and assigning taking thier value and adding

# The calories to finalize total food_calories

total_cal=0

calPerItem={}

for item in detected_list:

    if item in food_calories:
    
        calories = food_calories[item]
        
        calPerItem[item] = calories
        
        total_cal= total_cal+calories
        
print("Detected classes with their Calories: \n")

print(detected_list) 

for itemm,caloriee in calPerItem.items(): 

#items() is a method used with dictionaries to access
#the key-value pairs. It returns a view object that displays a list of a dictionary's 
#key-value tuple pairs.

 print(f"{itemm}: {caloriee} Calories \n")
 
print(f"Total calories: {total_cal}")

# GUI

root = tk.Tk()

root.title("Calorie Estimation")

# Load the background image

background_image_path = "image.png"  # Change this to your image path

bg_image = Image.open(background_image_path)

bg_image = bg_image.resize((800, 600))  # Resize if needed

bg_photo = ImageTk.PhotoImage(bg_image)

# Create a canvas and add the background image

canvas = tk.Canvas(root, width=800, height=600)

canvas.pack(fill="both", expand=True)

canvas.create_image(0, 0, anchor="nw", image=bg_photo)

# Main heading

heading = Label(root, text="Calorie Estimation", font=("Helvetica", 24, "bold"), bg="royal blue", fg="white")

heading.place(relx=0.5, rely=0.1, anchor="n")

# Create a frame for the white box with a black border

border_thickness = 5

frame_width = 400

frame_height = 250

border_frame = tk.Frame(root, bg="black")

border_frame.place(relx=0.5, rely=0.3, anchor="n", width=frame_width + border_thickness, height=frame_height + border_thickness)

# Frame for the detected food items (inside the border frame)

frame = tk.Frame(border_frame, bg="white")

frame.place(relx=0.5, rely=0.5, anchor="center", width=frame_width, height=frame_height)

# Showing the labels of Food items and calories on GUI  

for index, (food, calories) in enumerate(calPerItem.items()):

    label = Label(frame, text=f"{food}: {calories} Calories", font=("Helvetica", 12), bg="white")
    
    label.pack(anchor="w", padx=10, pady=2)

# Label for total calories

total_label = Label(frame, text=f"Total Calories: {total_cal}", font=("Helvetica", 14, "bold"), bg="white")

total_label.pack(anchor="w", padx=10, pady=10)

# Note about calories being based on 200g

note = Label(root, text="Calorie values are based on a 200g serving", font=("Helvetica", 10), bg="royal blue", fg="white")

note.place(relx=0.5, rely=0.9, anchor="s")

cv2.imshow("detections",detected_image)

cv2.waitKey()

cv2.destroyAllWindows()

# Run the application

root.mainloop()


#print(result.names) This is to show the result names cuz Results has different lists if we print it
#it shows that it has boxes,names and a lot of things so we use loop for result in results,,,and then 
#using that result.names we access the names and if we want to access boxes result.boxes are there and 
#in result.boxes we have confidence so we loop in that again like for detections in result.boxes and then
#by detections.cls[0] we access the conf of the detected object from the results.
