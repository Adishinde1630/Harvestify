# Harvestify

Flask web app for plant growth prediction, plant disease detection, and fertilizer recommendation.

Project layout (important files)

- `app/` — Flask package (application code)
- `wsgi.py` — WSGI entrypoint used by Gunicorn
- `requirements.txt` — Python dependencies
- `Procfile` — for Heroku deployment (`web: gunicorn wsgi:application`)
- `Dockerfile` — container image instructions
- `templates/`, `static/` — front-end assets
- `classifier.pkl`, `fertilizer.pkl`, `keras_model.h5`, `label_transform.pkl` — ML artifacts (not committed)

Quickstart (local)

1. Activate a Python virtual environment (use the included `tf-env` or create a new one):

```powershell
# Activate included venv (PowerShell)
& 'C:\Users\GORAKH\Desktop\Harvestify\tf-env\Scripts\Activate.ps1'

# Or create+activate a new venv from project root
python -m venv .venv
& 'C:\Users\GORAKH\Desktop\Harvestify\.venv\Scripts\Activate.ps1'
```

2. Install dependencies:

```powershell
pip install --upgrade pip
pip install -r requirements.txt
```

3. Generate ML artifacts if you don't have them (optional):

```powershell
# Train & save fertilizer model (writes classifier.pkl and fertilizer.pkl)
python fertilizer_train.py

# (Optional/expensive) Train plant disease model - requires Dataset/PlantVillage and significant time/GPU
python -c "from plant_disease import train_model; train_model(dataset_path='Dataset/PlantVillage', epochs=10)"
```

4. Run locally (development):

```powershell
# Run with Flask debug server (development)
python -m flask --app app run --port 5000

# Or run with Gunicorn (production-like)
gunicorn wsgi:application --bind 0.0.0.0:8000
```

Docker (recommended for production parity)

Build and run the container:

```powershell
docker build -t harvestify:latest .
docker run -p 8000:8000 --env SECRET_KEY='your_secret' --env DATABASE_URL='sqlite:///users.db' harvestify:latest
```

Heroku (one-line deploy)

1. Install the Heroku CLI and login: `heroku login`
2. Create an app and push:

```powershell
heroku create your-app-name
git push heroku main
heroku config:set SECRET_KEY='your_secret' DATABASE_URL='postgres://...'
```

Notes

- The app reads `SECRET_KEY` and `DATABASE_URL` from environment variables in production. Do not store secrets in code.
- ML artifacts (`*.pkl`, `*.h5`) are purposefully ignored by `.gitignore`; include them in your deployment via a secure storage (S3) or mount them into the container at runtime.
- If you deploy to Heroku, add a proper database (Postgres) and set `DATABASE_URL` accordingly.
- For GPU-heavy model training, run training outside the web dyno (use dedicated training environment).

Next steps (suggested)

- Add GitHub Actions to automatically build and push a Docker image.
- Add health checks and simple unit tests to validate endpoints.

