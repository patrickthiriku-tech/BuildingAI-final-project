# Nairobi GreenRoofs AI
Building AI course project

## Summary
Nairobi GreenRoofs AI uses satellite imagery and machine learning to find flat, underutilized rooftops across Nairobi (such as in Kilimani, Westlands, and the CBD) that can be converted into rooftop gardens. This helps lower neighborhood temperatures and manage rainwater runoff during heavy storms. Building AI course project.

## Background
Living in Nairobi, I've seen concrete high-rises rapidly replace trees and green spaces across areas like Kilimani, Westlands, and along Mombasa Road. During the hot months leading into the long rains, the heat bouncing off asphalt and dark roofs makes dense neighborhoods feel significantly hotter than greener areas near Karura Forest or Nairobi National Park.

When heavy rains arrive, all that concrete causes flash flooding on roads because water has nowhere to drain into the ground. Rooftops represent a huge amount of flat space across our city, but almost all of it sits completely empty.

* **Hard to map manually:** City officials cannot physically inspect thousands of buildings across Nairobi to see which roofs can support gardens.
* **Wasted rainwater & heat traps:** Unused dark roofs absorb heat all day and dump rainwater directly into already overflowing storm drains.
* **My goal:** Build a simple tool to automatically map roofs in Nairobi that are prime candidates for green retrofitting.

## How is it used?
This solution is designed for Nairobi City County planners, local property developers, and urban farming initiatives.

1. A user selects a specific neighborhood or sub-county in Nairobi (for example, Dagoretti North or Nairobi Central).
2. The model scans satellite images, detects building outlines, and filters out pitched iron-sheet (*mabati*) roofs to isolate flat concrete slabs.
3. An interactive map highlights candidate roofs with a "Green Potential Score" so local teams know exactly where to prioritize projects.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Roof_gardens.jpg/640px-Roof_gardens.jpg" width="400">

## Data sources and AI methods
The project relies on public satellite images and open geographic data:

* **Satellite Imagery:** [Copernicus Sentinel-2](https://sentinels.copernicus.eu/) for free multispectral images covering Nairobi.
* **Building Polygons:** [OpenStreetMap API](https://wiki.openstreetmap.org/wiki/API) for building footprints across the county.

| Pipeline Stage | Method | Purpose |
| ----------- | ----------- | ----------- |
| Roof Detection | Convolutional Neural Network (U-Net) | Locates buildings and separates flat concrete slabs from sloped roofs |
| Surface Check | CNN Classifier | Checks if a roof is already green or covered in heavy equipment |
| Ranking | Logistic Regression | Calculates a 0-100 score based on roof area and location |

```python
# Simple helper script to score roof usability in Nairobi
def check_roof_suitability(usable_area_sqm, is_flat_concrete, solar_exposure_ratio):
    # Filter out sloped iron roofs or tiny spaces
    if not is_flat_concrete or usable_area_sqm < 40:
        return 0.0
    
    # Simple weighted calculation
    area_score = min(usable_area_sqm / 300, 1.0) * 0.7
    sun_score = solar_exposure_ratio * 0.3
    
    final_score = (area_score + sun_score) * 100
    return round(final_score, 1)

# Example run for a site in Kilimani
print("Kilimani Candidate Score:", check_roof_suitability(180, True, 0.85))
