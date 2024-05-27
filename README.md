# IRON-PURITY

import cv2
import os

# Define the fixed size to which you want to resize the images
target_size = (224, 224)  # Example size, adjust according to your model's input size

# Path to directory containing your dataset
dataset_dir = 'path/to/your/dataset'

# Path to directory where processed images will be saved
output_dir = 'path/to/your/processed/images'

# Create the output directory if it doesn't exist
os.makedirs(output_dir, exist_ok=True)

# List of folders, each containing images of a different class
class_folders = [os.path.join(dataset_dir, folder) for folder in os.listdir(dataset_dir) if os.path.isdir(os.path.join(dataset_dir, folder))]

# Iterate through each class folder
for class_folder in class_folders:
    # Create a corresponding subdirectory in the output directory
    class_name = os.path.basename(class_folder)
    class_output_dir = os.path.join(output_dir, class_name)
    os.makedirs(class_output_dir, exist_ok=True)
    
    # List all image files in the class folder
    image_files = [os.path.join(class_folder, file) for file in os.listdir(class_folder) if file.endswith('.jpg')]
    
    # Process and save each image in the class folder
    for file_path in image_files:
        # Load the image
        img = cv2.imread(file_path)
        
        # Get original image dimensions
        original_height, original_width = img.shape[:2]
        
        # Calculate aspect ratio
        aspect_ratio = original_width / original_height
        
        # Resize the image while preserving aspect ratio
        if aspect_ratio > 1:  # Landscape orientation
            new_width = target_size[0]
            new_height = int(new_width / aspect_ratio)
        else:  # Portrait or square orientation
            new_height = target_size[1]
            new_width = int(new_height * aspect_ratio)
        
        resized_img = cv2.resize(img, (new_width, new_height))
        
        # Add padding to achieve the target size
        top_padding = (target_size[1] - new_height) // 2
        bottom_padding = target_size[1] - new_height - top_padding
        left_padding = (target_size[0] - new_width) // 2
        right_padding = target_size[0] - new_width - left_padding
        
        padded_img = cv2.copyMakeBorder(resized_img, top_padding, bottom_padding, left_padding, right_padding, cv2.BORDER_CONSTANT, value=(0, 0, 0))
        
        # Construct the output file path
        output_file = os.path.join(class_output_dir, os.path.basename(file_path))
        
        # Save the processed image
        cv2.imwrite(output_file, padded_img)

print("Processing complete.")
