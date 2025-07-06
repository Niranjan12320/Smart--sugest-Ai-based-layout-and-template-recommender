# Smart--sugest-Ai-based-layout-and-template-recommender
SmartLayout AI is an intelligent tool that recommends personalized design templates based on user content and intent using NLP and machine learning. It helps users quickly find the best layout, saving time and improving design quality.
// ==== 1. Show a simple input dialog (simulated) ====
const userInput = "product sale flyer"; // Simulated user input

// ==== 2. Display Recommended Layout Templates ====
const templates = ["Business Card", "Instagram Story", "Poster"];
templates.forEach(template => {
  console.log("Suggested Template:", template);
});

// ==== 3. Send Text Input to Backend ====
fetch("http://localhost:5000/api/suggest", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ text: userInput })
})
.then(res => res.json())
.then(data => console.log("Backend Suggestion:", data.suggestion));

// ==== 4. Basic Express Server ====
const express = require("express");
const cors = require("cors");
const app = express();
app.use(cors());
app.use(express.json());

// ==== 5. Template Recommendation Endpoint ====
app.post("/api/suggest", (req, res) => {
  const { text } = req.body;
  const suggestion = mapInputToTemplate(text);
  res.status(200).json({ success: true, suggestion });
});

// ==== 6. OpenAI API for Layout Suggestions ====
const { Configuration, OpenAIApi } = require("openai");
const configuration = new Configuration({ apiKey: process.env.OPENAI_API_KEY });
const openai = new OpenAIApi(configuration);

async function getOpenAISuggestion(input) {
  const response = await openai.createCompletion({
    model: "text-davinci-003",
    prompt: `Suggest a layout type for the content: ${input}`,
    max_tokens: 50
  });
  return response.data.choices[0].text.trim();
}

// ==== 7. Log User Input to Firebase ====
const { getFirestore, collection, addDoc } = require("firebase/firestore");
const db = getFirestore();

async function logUserInputToFirebase(input) {
  await addDoc(collection(db, "user_logs"), {
    input,
    timestamp: new Date()
  });
}

// ==== 8. Simple Keyword-to-Template Mapping ====
function mapInputToTemplate(input) {
  if (input.includes("event")) return "Event Poster";
  if (input.includes("product")) return "Product Ad";
  if (input.includes("sale")) return "Ad Banner";
  return "Generic Template";
}

// ==== 9. Enable CORS ====
app.use(cors()); // Already included above

// ==== 10. Run the Server ====
const PORT = 5000;
app.listen(PORT, () => console.log(`SmartSuggest server running on port ${PORT}`));
