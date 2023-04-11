==========
3D Starmap 
==========

:Date: 2023-04-10

Start Giotto
============

To run the current vignette you need to install the Giotto Suite branch.

.. container:: cell

   .. code:: r
   
      # Ensure Giotto Suite is installed.
      if(!"Giotto" %in% installed.packages()) {
        devtools::install_github("PratishthaGuckhool/Giotto@suite_dev")
      }

      library(Giotto)
      
Create Giotto object
====================

Minimum requirements:

1. Matrix with expression information (or path to)
2. x,y(,z) coordinates for cells or spots (or path to)

.. container:: cell

   .. code:: r 
      
      temp_dir = getwd()
      myinstructions = createGiottoInstructions(save_dir = temp_dir,
                                                 save_plot = FALSE,
                                                 show_plot = TRUE)

      expr_path = paste0(temp_dir, "/STARmap_3D_data_expression.txt")
      loc_path = paste0(temp_dir, "/STARmap_3D_data_cell_locations.txt")
      starmap_mini = createGiottoObject(expression = expr_path,
                                        spatial_locs = loc_path,
                                        instructions = myinstructions)
  
      # show instructions associated with giotto object (starmap_mini)
      showGiottoInstructions(starmap_mini)
      
Processing steps
================

1. Filter genes and cells based on detection frequencies

2. Normalize expression matrix (log transformation, scaling factor and/or z-scores)

3. Add cell and gene statistics (optional)

4. Adjust expression matrix for technical covariates or batches (optional). These results will be stored in the custom sl

.. container:: cell

   .. code:: r
   
      filterDistributions(starmap_mini, detection = 'cells',
                          save_param = list(save_name = '2_a_filtergenes'))
 
      filterCombinations(starmap_mini,
                         expression_thresholds = c(1),
                         feat_det_in_min_cells = c(50, 100, 200),
                         min_det_genes_per_cell = c(20, 28, 28),
                         save_param = list(save_name = '2_c_filtercombos'))

      starmap_mini = filterGiotto(gobject = starmap_mini,
                                  expression_threshold = 1,
                                  feat_det_in_min_cells = 50,
                                  min_det_genes_per_cell = 20,
                                  expression_values = c('raw'),
                                  verbose = T)

      starmap_mini = normalizeGiotto(gobject = starmap_mini,
                                     scalefactor = 6000, verbose = T)

      starmap_mini = addStatistics(gobject = starmap_mini)
      
Dimension Reduction
===================

1. Identify highly variable genes (HVG)

2. Perform PCA

3. Identify number of significant prinicipal components (PCs)

4. Run UMAP and/or TSNE on PCs (or directly on matrix)

.. container:: cell

   .. code:: r
   
      starmap_mini <- runPCA(gobject = starmap_mini, method = 'factominer')
      screePlot(starmap_mini, ncp = 30,
                save_param = list(save_name = '3_a_screeplot'))

      plotPCA(gobject = starmap_mini,
              save_param = list(save_name = '3_b_PCA'))

      # 2D umap
      starmap_mini <- runUMAP(starmap_mini, dimensions_to_use = 1:8)
      plotUMAP(gobject = starmap_mini,
               save_param = list(save_name = '3_c_UMAP'))

      # 3D umap
      starmap_mini <- runUMAP(starmap_mini, dimensions_to_use = 1:8, name = '3D_umap', n_components = 3)
      plotUMAP_3D(gobject = starmap_mini, dim_reduction_name = '3D_umap',
                  save_param = list(save_name = '3_d_UMAP_3D'))

      # 2D tsne
      starmap_mini <- runtSNE(starmap_mini, dimensions_to_use = 1:8)
      plotTSNE(gobject = starmap_mini,
               save_param = list(save_name = '3_e_TSNE'))
               
Spatial Network
===============

Only the method = delaunayn_geometry can make 3D Delaunay networks. This requires the package geometry to be installed.

1. Visualize information about the default Delaunay network

2. Create a spatial Delaunay network (default)

3. Create a spatial kNN network

.. container:: cell

   .. code:: r
   
      plotStatDelaunayNetwork(gobject = starmap_mini, maximum_distance = 200,
                              method = 'delaunayn_geometry',
                              save_param = list(save_name = '8_aa_delnetwork'))

      starmap_mini = createSpatialNetwork(gobject = starmap_mini, minimum_k = 2,
                                          maximum_distance_delaunay = 200,
                                          method = 'Delaunay',
                                          delaunay_method = 'delaunayn_geometry')
      starmap_mini = createSpatialNetwork(gobject = starmap_mini, minimum_k = 2,
                                          method = 'kNN', k = 10)

      showGiottoSpatNetworks(starmap_mini)
      
3D visualization in Spatial and Expression Space
================================================

.. container:: cell

   .. code:: r
   
      # Create a virtual 2D cross section
      cross_section = createCrossSection(gobject = starmap_mini)
      
      
      

