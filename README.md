# Magic: The Gathering Image Classifier

A computer vision and machine learning project that identifies Magic: The Gathering cards from images and tracks their values in a personal collection catalog.

## Project Overview

This project uses a combination of computer vision, optical character recognition (OCR), and vector database technology to:

1. **Download and prepare MtG card data** - Fetches card images and metadata from Scryfall API
2. **Extract text from cards** - Uses OCR to read card names and text from images
3. **Process user-uploaded images** - Detects, crops, and rotates cards from photos
4. **Match cards using AI** - Embeds images using EfficientNetB0 and stores them in a Pinecone vector database
5. **Track collection value** - Maintains a CSV catalog with card details, quantities, and current prices

## Features

- **Image-based card identification** using deep learning embeddings
- **OCR text extraction** for improved search accuracy
- **Automatic image preprocessing** including card detection, cropping, and rotation
- **Vector similarity search** using Pinecone for fast, accurate matching
- **Collection management** with automatic price tracking
- **Batch processing** for efficient database population

## Project Structure

```
MtG_Image_Classifier/
├── MtG_Data_Prep.ipynb              # Downloads card data and images from Scryfall
├── OCR.ipynb                        # Optical Character Recognition experiments
├── User_Image_Handler.ipynb         # Image preprocessing and card detection
├── Vector_Database_Setup.ipynb      # Vector database creation and querying
├── Automated_Card_Feeder.ipynb      # High-speed automated processing (desktop)
├── Mobile_PWA_Implementation.ipynb  # Mobile Progressive Web App implementation
├── Datasets/
│   ├── default-cards-20240817211226.json  # Scryfall card metadata
│   ├── mtg_images/                 # Downloaded card reference images
│   ├── mtg_test_images/            # Test images for validation
│   └── test_processed_image_output/ # Processed image outputs
├── Output/
│   └── mtg_catalog.csv             # Personal collection catalog
└── Mobile_PWA/                     # Progressive Web App files
    ├── index.html                  # Main PWA interface
    ├── manifest.json               # PWA manifest
    ├── sw.js                       # Service worker
    ├── mtg_card_feeder_api.py      # Backend API server
    └── deployment_guide.md         # Deployment instructions
```

## Prerequisites

### Required Software

1. **Python 3.8+**
2. **Tesseract OCR** - Download from: https://github.com/tesseract-ocr/tesseract
   - Install to: `C:\Program Files\Tesseract-OCR\tesseract.exe`
3. **Jupyter Notebook** or **VS Code** with Jupyter extension

### API Keys Required

1. **Pinecone API Key** - Sign up at https://www.pinecone.io/
   - Set environment variable: `PINECONE_API_KEY=your_api_key_here`

### Python Dependencies

Install the following packages:

```bash
pip install jupyter
pip install opencv-python
pip install pillow
pip install pytesseract
pip install tensorflow
pip install pinecone-client
pip install numpy
pip install requests
pip install pandas
```

## Setup Instructions

### 1. Environment Setup

```powershell
# Clone or download the project
cd "c:\Users\ericb\Desktop\Projects\MtG_Image_Classifier"

# Install Python dependencies
pip install jupyter opencv-python pillow pytesseract tensorflow pinecone-client numpy requests pandas

# Set your Pinecone API key as an environment variable
$env:PINECONE_API_KEY="your_pinecone_api_key_here"
```

### 2. Install Tesseract OCR

1. Download Tesseract from: https://github.com/tesseract-ocr/tesseract/wiki/Downloads
2. Install to the default location: `C:\Program Files\Tesseract-OCR\`
3. Verify the path in `OCR.ipynb` matches your installation

### 3. Data Preparation (First Time Setup)

**Note: This step downloads ~99,000 card images and takes 15+ hours**

1. Open `MtG_Data_Prep.ipynb`
2. Run all cells to:
   - Load Scryfall card data
   - Filter out cards with missing images
   - Download all card images (rate-limited to 10 images/second)

### 4. Vector Database Setup

1. Open `Vector_Database_Setup.ipynb`
2. Run all cells to:
   - Initialize Pinecone vector database
   - Generate embeddings using EfficientNetB0
   - Upload all card embeddings to Pinecone

## How to Use

### Identifying Cards from Photos

1. **Take a photo** of your Magic cards with your phone
2. **Save the image** to the `Datasets/mtg_test_images/` folder
3. **Open** `Vector_Database_Setup.ipynb`
4. **Update the image path** in the testing section:
   ```python
   test = embed_image('Datasets/mtg_test_images/your_image.jpg')
   ```
5. **Run the query cells** to get the top matching cards
6. **Add to catalog** by running the CSV update cells

### Processing User Images

For better results with phone photos:

1. **Open** `User_Image_Handler.ipynb`
2. **Use the card detection functions** to:
   - Automatically detect card boundaries
   - Crop individual cards from photos
   - Rotate cards to standard orientation

### OCR Text Extraction

1. **Open** `OCR.ipynb`
2. **Use the text extraction functions** to:
   - Extract card names from images
   - Get all text from cards for better matching
   - Preprocess images for better OCR accuracy

## Key Functions

### Image Processing
- `detect_and_crop_outer_card()` - Detects and crops card from photo
- `extract_card_name_from_segment()` - OCR extraction of card names
- `preprocess_image_with_padding()` - Prepares images for embedding

### Vector Database
- `embed_image()` - Creates EfficientNetB0 embeddings
- `batch_upsert_embeddings()` - Uploads embeddings to Pinecone
- `get_card_details()` - Fetches card data from Scryfall API

### Collection Management
- `add_card_data_to_csv()` - Updates collection catalog
- Tracks: card details, quantities owned, current prices, total values

## Output

The system generates a `mtg_catalog.csv` file with columns:
- `id` - Unique card identifier
- `name` - Card name
- `mana_cost` - Mana cost
- `type_line` - Card type
- `set_name` - Set name
- `rarity` - Card rarity
- `price` - Current USD price
- `number_owned` - Quantity in collection
- `total_value` - Total value of owned copies

## Performance Notes

- **Initial setup**: 15+ hours to download all card images
- **Vector database**: ~1-2 hours to process all embeddings
- **Card identification**: ~1-2 seconds per query
- **Database size**: ~99,000 card images (~10GB storage)

## Troubleshooting

### Common Issues

1. **Tesseract not found**
   - Verify installation path in `OCR.ipynb`
   - Ensure Tesseract is in your system PATH

2. **Pinecone connection errors**
   - Check your API key is set correctly
   - Verify internet connection

3. **Out of memory errors**
   - Reduce batch size in `batch_upsert_embeddings()`
   - Process images in smaller chunks

4. **Poor card detection**
   - Ensure good lighting in photos
   - Use solid, contrasting backgrounds
   - Keep cards flat and fully visible

### Performance Optimization

- Use GPU-enabled TensorFlow for faster embedding generation
- Adjust batch sizes based on available memory
- Consider using smaller image sizes for faster processing

## Future Improvements

- Integration with real-time price APIs
- Mobile app interface
- Support for foil/variant detection
- Batch photo processing
- Advanced image preprocessing for difficult lighting conditions

## Mobile Card Feeder Integration

For automated card processing using a phone-mounted card feeder system, a mobile-first architecture is recommended:

### Mobile Progressive Web App (PWA) Approach

**Why PWA Over Native App:**
- **Universal compatibility** - works on iOS and Android
- **No app store deployment** - instant access via web browser
- **Camera API access** - native camera control and video streaming
- **Real-time processing** - immediate feedback to users
- **Easy updates** - no app store approval process

### Processing Architecture Options

#### Option 1: Hybrid Processing (Recommended)
- **Mobile**: Real-time video capture, motion detection, frame preprocessing
- **Backend**: Heavy AI processing (embeddings, database queries)
- **Benefits**: Fast response, reliable processing, works on any phone

#### Option 2: On-Device Processing
- **Mobile**: Complete processing pipeline using TensorFlow.js
- **Benefits**: No internet required, instant results, privacy
- **Limitations**: Battery drain, slower on older phones, large model download

### Video Stream Processing (Core Technology)

**Why Video Over Timed Photos:**
- **Eliminates timing synchronization issues** between camera and feeder
- **Captures multiple frames** per card for better quality selection
- **Reduces file I/O overhead** compared to saving individual images
- **Enables motion detection** to trigger processing only when cards are present

### Performance Optimizations

#### 1. Hardware Acceleration
```python
# Use GPU-accelerated TensorFlow
import tensorflow as tf
physical_devices = tf.config.list_physical_devices('GPU')
if physical_devices:
    tf.config.experimental.set_memory_growth(physical_devices[0], True)
```

#### 2. Model Optimization
```python
# Convert EfficientNetB0 to TensorFlow Lite for faster inference
converter = tf.lite.TFLiteConverter.from_keras_model(base_model)
converter.optimizations = [tf.lite.Optimize.DEFAULT]
tflite_model = converter.convert()
```

#### 3. Batch Processing Pipeline
```python
# Process multiple cards in batches rather than individually
def process_card_batch(card_images, batch_size=8):
    embeddings = []
    for i in range(0, len(card_images), batch_size):
        batch = card_images[i:i+batch_size]
        batch_embeddings = base_model.predict(np.array(batch))
        embeddings.extend(batch_embeddings)
    return embeddings
```

#### 4. Asynchronous Processing
```python
import asyncio
import concurrent.futures

async def process_card_async(card_image):
    loop = asyncio.get_event_loop()
    with concurrent.futures.ThreadPoolExecutor() as executor:
        embedding = await loop.run_in_executor(executor, embed_image, card_image)
        result = await loop.run_in_executor(executor, query_database, embedding)
        return result
```

### Recommended Architecture

#### Real-Time Video Processing Pipeline:
1. **Video Capture** - Continuous video stream from camera
2. **Motion Detection** - Detect when card enters frame
3. **Frame Extraction** - Capture best quality frame(s)
4. **Preprocessing** - Crop, rotate, enhance (< 100ms)
5. **Embedding Generation** - Neural network inference (< 500ms)
6. **Database Query** - Vector similarity search (< 100ms)
7. **Result Processing** - Update catalog (< 50ms)
8. **Total Processing Time** - Target < 750ms per card

#### Hardware Recommendations:
- **GPU**: RTX 3060 or better for TensorFlow acceleration
- **CPU**: Intel i7/AMD Ryzen 7 with 8+ cores
- **RAM**: 16GB+ for model caching and batch processing
- **Storage**: NVMe SSD for fast image I/O
- **Camera**: High-resolution USB 3.0 camera (1080p+ at 30fps)

### Mobile PWA Implementation Strategy

#### Phase 1: Progressive Web App Setup
```javascript
// Service Worker for PWA functionality
// Camera access and video streaming
// WebRTC for real-time video processing
// WebAssembly for client-side optimizations
```

#### Phase 2: Real-Time Video Processing
```javascript
// WebRTC MediaStream API for camera access
// Canvas API for frame extraction and preprocessing
// WebSocket connection for backend communication
// IndexedDB for offline storage and caching
```

### Performance Optimizations

#### 1. Mobile-Optimized Processing
```python
# Lightweight model serving via TensorFlow Serving
# Edge computing deployment options
# Optimized API endpoints for mobile requests
```

#### 2. Client-Side Preprocessing
```javascript
// Canvas-based image preprocessing
// Motion detection using JavaScript
// Frame quality assessment before upload
// Automatic exposure and focus optimization
```

#### 3. Efficient Data Transfer
```python
# Image compression before upload
# WebSocket streaming for real-time communication
# Batch processing for multiple cards
# Progressive loading of results
```

### Mobile PWA Architecture

#### Frontend (Progressive Web App):
1. **Camera Control** - Video stream capture and settings
2. **Motion Detection** - JavaScript-based card detection
3. **Frame Processing** - Client-side preprocessing and quality checks
4. **Real-time Upload** - Stream frames to backend via WebSocket
5. **Results Display** - Live card identification and catalog updates
6. **Offline Mode** - Cache results when internet unavailable

#### Backend (Python API):
1. **WebSocket Server** - Real-time frame receiving
2. **AI Processing** - EfficientNet embeddings and Pinecone queries
3. **Database Management** - Card catalog and collection tracking
4. **Price Updates** - Real-time pricing from Scryfall API
5. **Export Features** - CSV, PDF, and sharing capabilities

### Recommended Technology Stack

#### Frontend (PWA):
- **Framework**: Vanilla JavaScript or React PWA
- **Video Processing**: WebRTC MediaStream API
- **Real-time Communication**: WebSocket or Socket.io
- **Offline Storage**: IndexedDB with Dexie.js
- **UI Framework**: Tailwind CSS for mobile-first design

#### Backend (API):
- **Framework**: FastAPI (Python) for high performance
- **WebSocket**: FastAPI WebSocket support
- **AI Processing**: TensorFlow Serving or direct inference
- **Database**: Pinecone + PostgreSQL for metadata
- **Deployment**: Docker containers on cloud platforms

### Mobile-Specific Optimizations

#### Camera and Video:
- **Auto-focus control** for sharp card images
- **Exposure compensation** for varying lighting
- **Frame rate optimization** (15-30 FPS based on device)
- **Resolution scaling** based on device capabilities

#### User Experience:
- **Visual feedback** - card detection overlay
- **Progress indicators** - processing status
- **Haptic feedback** - vibration on successful scan
- **Audio cues** - sound notifications for card detection

#### Performance:
- **Adaptive quality** - adjust processing based on device performance
- **Battery optimization** - reduce CPU usage when possible
- **Network awareness** - adjust upload quality based on connection
- **Memory management** - efficient frame buffer handling

### Mobile Implementation Checklist

#### PWA Development:
- [ ] **Camera permissions** - request and handle gracefully
- [ ] **Video stream setup** - MediaStream API integration
- [ ] **Motion detection** - JavaScript-based card detection
- [ ] **Frame extraction** - Canvas API for image capture
- [ ] **Real-time upload** - WebSocket streaming to backend
- [ ] **Offline support** - Service Worker and caching
- [ ] **Mobile UI** - Touch-friendly interface design
- [ ] **Performance monitoring** - FPS and processing time tracking

#### Backend API:
- [ ] **WebSocket server** - real-time frame processing
- [ ] **AI model serving** - optimized inference pipeline
- [ ] **Database integration** - Pinecone and catalog management
- [ ] **API rate limiting** - prevent abuse and ensure stability
- [ ] **Error handling** - robust failure recovery
- [ ] **Authentication** - user accounts and data privacy
- [ ] **Deployment** - cloud hosting with auto-scaling
- [ ] **Monitoring** - logging and performance metrics

### Hardware Recommendations for Card Feeder

#### Phone Mount Requirements:
- **Stable positioning** - minimize camera shake
- **Adjustable height** - accommodate different phone sizes
- **Good lighting** - LED strips or ring light integration
- **Card guidance** - rails or guides for consistent positioning

#### Optimal Phone Specifications:
- **Camera**: 12MP+ with good low-light performance
- **Processor**: Modern CPU (iPhone 12+, Android Snapdragon 855+)
- **RAM**: 4GB+ for smooth video processing
- **Network**: WiFi 5+ or 4G LTE for fast uploads

### Expected Performance (Mobile System)

#### Processing Pipeline Timing:
- **Motion detection**: < 50ms (client-side)
- **Frame capture/preprocessing**: < 100ms (client-side)
- **Upload to backend**: < 200ms (depends on network)
- **AI inference**: < 500ms (backend)
- **Database query**: < 100ms (backend)
- **Response to client**: < 100ms
- **Total end-to-end**: < 1 second per card

#### Throughput Targets:
- **Sustained rate**: 1 card every 2 seconds
- **Peak rate**: 1 card per second (short bursts)
- **Battery life**: 2+ hours continuous scanning
- **Accuracy**: 95%+ with proper lighting and positioning

### Error Handling & Reliability

```python
class RobustCardProcessor:
    def __init__(self):
        self.max_retries = 3
        self.timeout_seconds = 1.5
        
    def safe_process_card(self, frame):
        for attempt in range(self.max_retries):
            try:
                start_time = time.time()
                result = self.identify_card(frame)
                
                if time.time() - start_time > self.timeout_seconds:
                    logging.warning(f"Processing timeout on attempt {attempt + 1}")
                    continue
                    
                return result
                
            except Exception as e:
                logging.error(f"Processing error on attempt {attempt + 1}: {e}")
                if attempt == self.max_retries - 1:
                    return None  # Skip this card
```

### Expected Performance Gains

- **Video processing**: 3-5x faster than file-based approach
- **GPU acceleration**: 2-3x faster embedding generation
- **Batch processing**: 1.5-2x improvement for multiple cards
- **Model optimization**: 1.5-2x faster inference
- **Overall target**: Process 1 card every 1-2 seconds reliably

This approach should easily handle your 2-second target while providing room for more complex processing if needed.

## License

This project is for educational and personal use. Card data and images are provided by Scryfall.com under their terms of service.
