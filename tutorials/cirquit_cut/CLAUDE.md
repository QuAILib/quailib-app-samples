# Transportation Circuit-Cut Tutorial Rules

## Scope

The detailed specification for this tutorial is:

`tutorials/cirquit_cut/DEVELOPMENT_SPEC.md`

Read that file completely before planning or implementing any work in this directory.

## Non-negotiable rules

- Do not modify `tutorials/cirquit_cut/transportation_curcuit_cut_tutorial.ipynb`.
- Do not modify `tutorials/qklearn_fmqa/**`.
- Do not create or edit the improved notebook until the Python experiment pipeline and formal reconstruction validation are complete.
- Perform mathematical validation, parameter search, cut selection, fragment generation, execution, reconstruction, and metrics in `.py` modules.
- The final notebook is a thin, reproducible teaching layer generated from stable Python modules and frozen result files.
- Use only the Python executable in `CIRCUIT_CUT_PYTHON`. Do not use the qklearn virtual environment, Conda base, system Python, `pip --user`, or an unapproved environment.
- Do not install packages from notebook cells.
- Do not use a mode-vote or bit-string concatenation heuristic as a formal circuit-cut reconstruction.
- Do not silently ignore required fragment bundles, coefficients, boundary conditions, or reconstruction terms.
- Do not silently replace the current circuit-cutting library with another library. If the current library cannot provide a formal reconstruction path, stop and report the exact limitation and available options.
- Keep private/internal API usage out of the notebook. If unavoidable, isolate it in a compatibility module, pin the source commit, and add API contract tests.
- Do not claim quantum advantage or solver superiority from this tutorial.
- Do not run `git commit` or `git push` unless the user explicitly requests it.
- Do not run destructive Git commands such as `git reset --hard` or `git clean`.
- When a gate fails, fix the Python modules or stop with a clear report. Do not apply one-off notebook-only patches.
