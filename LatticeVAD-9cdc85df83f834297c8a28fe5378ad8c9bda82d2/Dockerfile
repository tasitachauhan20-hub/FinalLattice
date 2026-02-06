# Stage 1: Build React frontend
FROM node:20-alpine AS frontend

WORKDIR /frontend

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source
COPY . .

# Build the React app
RUN npm run build

# Stage 2: Python backend with frontend
FROM python:3.11-slim

WORKDIR /app

# Install system deps for librosa (librosa needs libsndfile)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libsndfile1 \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements and install
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy app code
COPY . /app

# Copy built frontend from builder stage
COPY --from=frontend /frontend/dist ./dist

# Expose port
ENV PORT=8080

# Command
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
