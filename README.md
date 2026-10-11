# Harvest — Food Waste Recovery

A Spring Boot REST API with a responsive web dashboard for sharing surplus food, reviewing safety checks, and tracking distribution.

## Requirements

- Java 17 or newer
- Maven 3.6 or newer

## Upload to GitHub

1. Create a repository named `food-waste-java` in your GitHub account. Choose public or private as you prefer.
2. Install Git for Windows and open PowerShell in this project folder.
3. Run these commands (replace `YOUR-GITHUB-USERNAME` with your GitHub username):

```powershell
git init
git add .
git status
git commit -m "Initial Harvest food recovery website"
git branch -M main
git remote add origin https://github.com/YOUR-GITHUB-USERNAME/food-waste-java.git
git push -u origin main
```

Review `git status` before committing. `.gitignore` excludes local environment files, secrets, and build output. Never commit an actual API key. GitHub Actions runs the Maven verification workflow on pushes and pull requests.

If you prefer, create the GitHub repository first and use GitHub's website to upload the project files, ensuring `.env` files and `target/` are not uploaded.

## Run locally

From this project directory:

```sh
mvn spring-boot:run
```

Open [http://localhost:8080](http://localhost:8080). The dashboard and API are served by the same Spring Boot application.

To build and run the packaged application:

```sh
mvn clean package
java -jar target/food-waste-java-0.0.1-SNAPSHOT.jar
```

## Website features

- Create food listings with category, description, quantity, pickup location, and optional image URL
- Browse, search, and filter listings by status or category
- Update a listing's workflow status and safety check
- Delete listings with confirmation
- View live totals for pending, in-progress, and distributed food
- Search the public OpenStreetMap directory for mapped food banks and community centres within 20 km of Jaipur
- Request optional Google Gemini suggestions for listing category and coordination steps

## Optional integrations

### Jaipur public map directory

The “Find organizations” button queries OpenStreetMap's public Overpass API for mapped food banks and community centres near central Jaipur. These are map records only, not confirmed partners or live surplus-food offers. Verify each organization and its donation process directly. Results may be incomplete, and the app displays OpenStreetMap attribution.

### Gemini listing helper

The optional listing helper sends the description, category, and quantity entered in the form to Google Gemini. Do not include personal or sensitive information. Suggestions are not stored by Harvest unless you choose to apply details and submit the listing. AI output is not food-safety advice; a person must verify all information.

This is a per-request assistant, not a model trained or fine-tuned on your data, and it does not keep conversational memory.

Create a Gemini API key in Google AI Studio, then set it in the server process environment. Never paste the key into source code, browser JavaScript, or a public repository.

`.env.example` documents the optional settings but is not loaded automatically by Spring Boot. Setting the variables in the process environment (as in the command below) is the simplest local setup. You do not need a Gemini key to upload the project or use the rest of the app; the OpenStreetMap directory also needs no API key.

PowerShell for the current terminal:

```powershell
$env:GEMINI_API_KEY = "your-key"
mvn spring-boot:run
```

The key is optional. Without it, the rest of the app works and the AI endpoint returns an explicit configuration error. The model can be changed with `GEMINI_MODEL`.

Do not expose the application publicly without adding user authentication and AI request limits. CORS is restricted to `http://localhost:8080` by default; set `APP_CORS_ALLOWED_ORIGIN` to the exact deployed website origin when needed.

## API

The browser app calls the same-origin `/api/food-waste` API. Useful routes:

| Method | Route | Purpose |
| --- | --- | --- |
| `GET` | `/api/food-waste` | List all food |
| `GET` | `/api/food-waste/{id}` | Get one listing |
| `POST` | `/api/food-waste` | Create a listing |
| `PUT` | `/api/food-waste/{id}/status` | Update workflow status |
| `PUT` | `/api/food-waste/{id}/safety-check` | Update safety check |
| `GET` | `/api/food-waste/category/{category}` | Filter by category |
| `GET` | `/api/food-waste/safety-check/{safetyCheck}` | Filter by safety check |
| `GET` | `/api/food-waste/status/pending` | Get pending listings |
| `GET` | `/api/food-waste/organizations` | Search public Jaipur map listings |
| `POST` | `/api/food-waste/ai/suggest` | Ask Gemini for optional listing suggestions |
| `DELETE` | `/api/food-waste/{id}` | Delete a listing |

AI request example:

```json
{
  "description": "A crate of ripe tomatoes",
  "quantity": "About 8 kg",
  "category": "fruit-vegetable"
}
```

The default H2 database is in-memory, so listings reset when the application stops. The H2 console is available at `/h2-console` during local development.
