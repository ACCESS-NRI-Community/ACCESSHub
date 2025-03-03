# ACCESS-RAM 

<!---
![ACCESS RAM model](/assets/model-config-logos/configurations-without-titles/access-ram.png){: class="img-contain white-background round-edges with-padding intro-img" loading="lazy"}
-->

The ACCESS-Regional Area Model (ACCESS-RAM) is an implementation of the [Unified Model (UM)](https://www.metoffice.gov.uk/research/approach/modelling-systems/unified-model) regional nesting suite with a consistent method of deployment, ancillary generation and workflow that gives Australian researchers improved capability for regional high-resolution atmosphere modelling on Gadi. Comprising [atmosphere](/models/model_components/atmosphere) and [land](/models/model_components/land) components, ACCESS-RAM overcomes the barrier of needing operational land-surface initial conditions to run the UM by allowing Australian Researchers to seamlessly use initial land-surface conditions from other sources.<br>
 
Like many other regional models, ACCESS-RAM requires lateral boundary conditions in addition to initial conditions. The model can also provide both initial and lateral boundary (“driving”) conditions for smaller domains in a “nesting” formation. "Nesting" is essentially the process of embedding a smaller, finer resolution region within a larger, coarser-resolution domain to achieve greater accuracy for localised weather events. Information such as temperature, pressure, and wind speed from the larger domain are used as input for the nested region, allowing more accurate simulation of conditions within the nest. <br>


### ACCESS-RAM3 
ACCESS-RAM3 has been set up to run with [ECMWF Reanalysis v5 (ERA5)](https://www.ecmwf.int/en/forecasts/dataset/ecmwf-reanalysis-v5) for both the initial conditions and driving conditions at the lateral boundaries. There are also options to replace the land-surface initial conditions and standard UM nesting options. The replacement options for land-surface initial conditions are: [ERA5-land](https://www.ecmwf.int/en/era5-land) or [BARRA-R2](http://www.bom.gov.au/research/publications/researchreports/BRR-067.pdf).<br>   

While ACCESS-RAM3 has been configured with a default two-level nest, subsequent level nests are also possible. When replacing land-surface initial conditions with ERA5-land or BARRA-R2, the spatial resolution and placement of grid points within the first-level nest are constrained to be a subset of the replacement dataset (i.e., 0.1° for ERA5-land or 0.11° for BARRA-R2). The spatial resolution of the second-level (inner) nest has a default value of 0.0198°. Both the spatial resolution and placement of the second and subsequent level nests are, however, configurable.<br> 

### Model components 

**Atmosphere and land**:
   - Nests with spatial resolution > 4km: [GAL9 science configuration](https://doi.org/10.5194/gmd-12-1909-2019) (70 vertical levels)  

   - Nests with spatial resolution ≤ 4km: [RAL3.2 science configuration](https://10.5194/gmd-2024-201) (90 vertical levels)  