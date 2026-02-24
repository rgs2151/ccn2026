# CCN2026 Figures Workflow

## Structure
Each figure function follows this pattern:
```python
def figure_Xx(args, cache_path='cache/figXx.pkl', recompute=False):
    def process():
        # All computation here
        return data_dict
    
    # Cache logic
    if not recompute and os.path.exists(cache_path):
        with open(cache_path, 'rb') as f:
            data = pickle.load(f)
    else:
        os.makedirs(os.path.dirname(cache_path), exist_ok=True)
        data = process()
        with open(cache_path, 'wb') as f:
            pickle.dump(data, f)
    
    # Plotting
    set_pub_style()
    fig, axes = plt.subplots(...)
    # ...
    return fig
```

## Data
- `all_sessions.h5` contains all session data
- Use `load_from_h5(h5_path, mouse_id, trial_id)` to load
- Returns: `place_maps`, `smooth_place_maps`, `trials_table`
- `trials_table` columns: `lap_index`, `context`, `reward_trial`, `water`, `lick_or_not`

## Styling
- `color_main = "black"` - primary data (full alpha)
- `color_alt = "darkred"` - secondary data (0.5 alpha)
- Solid `-` for main, dashed `--` for secondary
- Call `set_pub_style()` before plotting
- Use `prettify(ax, title, xlabel, ylabel)` for axis styling

## Metrics
- Hit rate: `(rw & lick).sum() / n_rw`
- CR rate: `(~rw & ~lick).sum() / n_nr`
- Performance: `(hits + crs) / total`

## Decoding
- Population decoding: SVM with linear kernel, LOOCV
- Features: `np.trapz(smooth_place_maps, axis=2).T` → (n_trials, n_neurons)
- Targets with masks:
  - A vs B: mask to AB trials only
  - C vs D: mask to CD trials only
  - Reward, Lick: all trials

## Conventions
- Minimal comments
- Keep functions self-contained
- Return `fig` from figure functions
- Cache to `cache/` directory
