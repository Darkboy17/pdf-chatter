# PDF Upload & Query Application

This application allows users to upload PDF files, query content within the uploaded files, and manage (view/delete) stored PDF documents. The backend is built with FastAPI and uses SQLite as a database for metadata storage. The frontend is built with React.

## Project Setup

### Prerequisites

-   **Python 3.9+** for the backend
-   **Node.js & npm** for the frontend
-   **SQLite** (integrated with Python)

### Backend Setup

1.  **Clone the repository** and navigate to the backend directory:

	    git clone <repo-url>
	    cd backend
2. **Create a virtual environment** and activate it:
	
	    python -m venv env
    	# On Windows, use venv\Scripts\activate
    	$ On Linux, use source env/bin/activate  
3. **Install dependencies** listed in `requirements.txt`:

	    pip install -r requirements.txt
	    
4. **Set up the SQLite Database**: The database is automatically created when the backend starts, with the necessary tables (`documents`) initialized if not present.

5. **Run the FastAPI server**:

	    uvicorn main:app --reload

### Frontend Setup

1.  **Navigate to the frontend directory**:

	    cd frontend
	    
2. **Install frontend dependencies**:

	    npm install
3. **Start the React development server**:

	    npm start
	    
The frontend will typically run at `http://localhost:3000`.

## Application Architecture

### Backend Structure

-   **FastAPI**: Handles REST API requests.
-   **SQLite Database**: Stores document metadata such as filename, hash, and upload date.
-   **PDF Processing**: Extracts text from uploaded PDFs using the `PyMuPDF` library.
-   **Vector Storage**: Each uploaded document’s content is stored in a vector format in `vector_stores`, making querying efficient.

### Frontend Structure

-   **React Components**:
    -   **UploadPDF**: Handles PDF upload.
    -   **PDFList**: Displays uploaded PDFs, allowing selection and deletion.
    -   **MessageInput**: Interface for querying PDF content.
-   **Axios**: Used for API requests to the backend.
-   **Toastify**: For error/success notifications.
-   **Tailwind CSS**: For styling.

## API Documentation

### Upload PDF

-   **Endpoint**: `/upload-pdf/`
-   **Method**: `POST`
-   **Description**: Uploads a PDF file to the server, extracts content, and stores metadata in the database.
-   **Parameters**:
    -   **file** (form-data): The PDF file to upload.
-   **Response**:
    -   **Success**:
		
		    {
			  "status": "success",
			  "document_id": "<document_id>",
			  "filename": "<filename>"
			}
	- **Duplicate PDF**:
	
		  {
			"message": "PDF has already been uploaded.",
			"duplicate": true
			}

### Delete PDF

-   **Endpoint**: `/delete-pdf/{document_id}`
-   **Method**: `DELETE`
-   **Description**: Deletes the specified PDF file and associated index data.
-   **Parameters**:
    -   **document_id** (URL path): ID of the PDF to delete.
-   **Response**:
    -   **Success**:
			
			    {
				  "status": "success",
				  "message": "<filename> and its associated index deleted successfully"
				}
	- **Error**:
	

		    {
			  "status": "error",
			  "message": "Document not found"
			}
### Get All Documents

-   **Endpoint**: `/documents`
-   **Method**: `GET`
-   **Description**: Retrieves metadata for all uploaded documents, excluding document content.
-   **Response**:
		

		    [
				  {
				    "id": 1,
				    "filename": "example.pdf",
				    "file_hash": "<hash>",
				    "uploaded_date": "2024-11-07"
				  },
				  ...
		]

## Notes

-   **Error Handling**: Error responses include detailed messages for easier debugging.
-   **Vector Indexing**: Each document is vectorized upon upload for faster retrieval and querying. Index data is stored in `vector_stores`.
-   **Security**: Only PDF files are accepted, and duplicates are prevented via hashing.
