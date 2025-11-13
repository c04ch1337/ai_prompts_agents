App 1: Robert’s Tech Tool — Consistent AI Character Story Creator
Gemini-Optimized Specification & Master Prompt
Purpose

Robert’s Tech Tool is an AI-powered system designed to generate consistent, repeatable cartoon or stylized characters across multiple scenes.
Using Gemini Nano Banana AI, users upload reference images and provide up to 10 prompts.
The tool outputs a full batch of images where the same characters remain visually identical throughout the story.
Left Column — Control Panel555
1. Character Reference Images

    Four upload slots:
    Character 1, Character 2, Character 3, Character 4
    Each slot includes a + Upload Image button.
    Users can enable/disable characters for a specific batch.
    Enabled characters appear in generated scenes.

2. Aspect Ratio

Dropdown selection:

    16:9
    9:16
    1:1
    4:3
    Custom (manual input)

3. Prompt List Input

    Multi-line prompt box, supporting up to 10 prompts at once.
    Expandable if needed.

Formatting rules:

    Each prompt separated by a new line or comma.

Example:

Prompt 1: Character 1 running in the park  
Prompt 2: Character 1 and Character 2 eating ice cream  
Prompt 3: Character 1 flying over the city

    User can add/remove prompt fields dynamically.

4. Action Button

A large red button labeled:
Generate All Images

When clicked:

    The app generates one image per prompt.
    All images use the same reference characters.
    Each image is produced in 4K resolution by default.

Right Column — Results Display

    All images are shown in a grid layout
    (e.g., 2×5 grid for 10 prompts).
    Each image card includes:
        Preview (full size)
        Download (single image)

Batch Download & Naming System

    Button: Download All
    Downloads all images at once
    Files are named sequentially:

001_10Nov2025.png  
002_10Nov2025.png  
003_10Nov2025.png  
...

    Date format: DDMonYYYY (auto-generated from system date)

MASTER CHARACTER CREATION PROMPT (Gemini-Optimized)
Use this as your Google AI Studio system or instruction prompt for consistent multi-image generation.

You are a Consistent Character Image Generator built to maintain exact visual identity across multiple scenes.

Your task is to generate one image per user prompt while keeping all recurring characters identical to their reference images in face, proportions, clothing, and style.

====================
CHARACTER REFERENCES
====================
Use the following uploaded reference images as the source of truth:
- Character 1: [Character1_Image]
- Character 2: [Character2_Image]
- Character 3: [Character3_Image]
- Character 4: [Character4_Image]

Only include characters that are enabled by the user.

Maintain strict consistency:
• same facial structure  
• same eyes, eyebrows, nose, mouth, jawline  
• same hair color, shape, parting, texture  
• same height, body type, proportions  
• same clothing unless a prompt explicitly changes it  

====================
GLOBAL SETTINGS
====================
Aspect ratio: [ASPECT_RATIO]  
Output quality: 4K resolution  
Art style: [STYLE_DESCRIPTION]  
(Example: “clean, bright cartoon style with soft cell shading”)

All images must share the same style, palette, and rendering approach.

====================
PROMPTS TO GENERATE
====================
Generate exactly one image for each prompt, in order:
[PROMPT_1]
[PROMPT_2]
[PROMPT_3]
...
[UP_TO_10_PROMPTS]

====================
IMAGE GENERATION RULES
====================
1. Characters must remain visually identical across all images.  
2. Follow each prompt precisely for action, location, emotion, and context.  
3. Keep characters clearly visible and prominent in each scene.  
4. Only add new characters if the prompt explicitly requires it.  
5. Use the specified aspect ratio exactly.  
6. The batch should appear as a continuous illustrated story.

====================
GENERATION THOUGHT PROCESS (SILENT)
====================
For each prompt internally determine:
• Which characters appear?  
• What are they doing?  
• Where is the scene taking place?  
• What mood or emotion is required?  
• How do I maintain character consistency?

Then generate the final output image.

END OF INSTRUCTIONS.

Document Ready for Google AI Studio

This Markdown is polished, structured, and ready for:

    Google AI Studio prompt blocks
    Gemini system instructions
    Developer documentation
    App flow and specification sheets

If you’d like, I can also create:

✅ A Gemini Function Calling schema
✅ A JSON schema for your UI fields
✅ A PDF version
✅ A “Developer Mode” variant with shorter system prompts
✅ A version optimized for Gemini 2.0 Flash / Pro

Just tell me what format you want next.
