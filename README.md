# Lost Wreck

Lost Wreck is a local research project to look for credible, potentially undocumented shipwreck candidates in public, high-resolution seafloor surveys.

The [project plan](PROJECT_PLAN.md) records the research method, constraints, and definition of done. The first step is to compare up to three surveys and confirm that one area has accessible numerical bathymetry. Record the decision in `reports/survey_selection.md` before building the viewer or detector.

## Local setup

Use Python 3.11 or newer. On Windows, create a virtual environment with the Python launcher. This example uses Python 3.12.

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe --version
```

On macOS or Linux, run `python3 -m venv .venv` with a compatible interpreter. Add application dependencies when the selected survey's format and access method are known.

## Initial investigation

- Timebox the investigation to approximately 8–12 hours and use free data and tools.
- Analyze one survey area. Aim to download under 300 MB and stay below the 1 GB ceiling. Store downloaded data under `data/`, which Git ignores.
- Record source URLs, survey identifiers, acquisition dates, coordinate reference systems, and processing parameters.
- Track shipwreck likelihood and evidence of possible novelty separately. Get approval before sharing sensitive coordinates, contacting authorities, or claiming an official discovery.
