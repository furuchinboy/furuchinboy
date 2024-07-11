from moviepy.editor import VideoFileClip
from fpdf import FPDF
import cv2
import pytesseract

# Function to extract key frames
def extract_key_frames(video_path, step=600):
    cap = cv2.VideoCapture(video_path)
    frames = []
    frame_rate = cap.get(cv2.CAP_PROP_FPS)
    frame_count = 0
    success, image = cap.read()

    while success:
        if frame_count % (frame_rate * step) == 0:
            frames.append(image)
        success, image = cap.read()
        frame_count += 1

    cap.release()
    return frames

# Function to save frames and create PDF
def frames_to_pdf(frames, pdf_path):
    pdf = FPDF()
    for i, frame in enumerate(frames):
        image_path = f"slide_{i+1}.jpg"
        cv2.imwrite(image_path, frame)
        pdf.add_page()
        pdf.image(image_path, x=10, y=10, w=190)
    pdf.output(pdf_path)

# Main process
video_path = "path/to/your/video.mp4"
pdf_path = "output/slides.pdf"

frames = extract_key_frames(video_path)
frames_to_pdf(frames, pdf_path)

print(f"PDF created at: {pdf_path}")
