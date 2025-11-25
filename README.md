# Nano Banana OOTD

![image](image.png)

A Vue 3 application that leverages Google's Nano Banana Pro (Gemini 3 Pro) model to generate detailed OOTD (Outfit of the Day) breakdowns and character concept sheets from user-uploaded photos.

## Features

- **OOTD Collage**: Generates a fashion magazine-style layout with itemized breakdown of the outfit.
- **Character Sheet**: Creates a detailed concept art character sheet with deconstruction of clothing, accessories, and personality traits.
- **AI-Powered**: Uses the advanced "Thinking" and vision capabilities of Gemini 3 Pro.
- **Secure**: API keys are stored locally in your browser.

## Tech Stack

- Vue 3
- Vite
- Tailwind CSS
- Google Generative AI SDK

## Setup

1. Navigate to the app directory:
   ```bash
   cd app
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Run the development server:
   ```bash
   npm run dev
   ```

## Usage

1. **API Key**: Enter your Google Gemini API Key in the top right corner. Ensure your key has access to the `gemini-3-pro-image-preview` model.
2. **Upload**: Drag and drop or select a photo of your outfit.
3. **Select Style**:
   - **OOTD Collage**: For a social media-ready fashion breakdown.
   - **Character Sheet**: For a detailed artistic deconstruction.
4. **Generate**: Click the generate button and wait for the AI to process and create your image.

## License

MIT
