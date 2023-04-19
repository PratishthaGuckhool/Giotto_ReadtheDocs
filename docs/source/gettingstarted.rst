.. _gettingstartedpage: 

#######################
Getting Started 
#######################

*************
Requirements
*************
* R (>= 3.5.1)
* Python (>= 3.0)
* `Windows, MacOS or Linux specific installation tools.`_

.. _Windows, MacOS or Linux specific installation tools.: https://support.rstudio.com/hc/en-us/articles/200486498-Package-Development-Prerequisites

.. _Installation_Section:

*************
Installation
*************
Giotto is installed via R **but there are required Python modules** which **must** be installed in order for Giotto to function properly. 
Below are the instructions for both the Giotto Package Installation (Part 1) done via R and the Installation of Required Python Modules (Part 2). 
In order to use Giotto successfully, users **must** have both the Giotto Package installed as well as the required Python Packages. 

.. important:: 

	Make sure that your system has satisfied **all** of the requirements needed to sucessfully install Giotto.

For ease of acces, there is a method which allows users to automatically install all of the necessary python modules from within R (see Part 2.2 Giotto-Specific Python Packages for more information). 

:octicon:`light-bulb` Checkout out the :ref:`Common Errors and Solutions <faqs>` for more troubleshooting tips on common installation issues.  

.. _GiottoInstallationWorkflow:

Giotto Installation Workflow
==============================

.. tab-set:: 

	.. tab-item:: Giotto Installation Workflow 

		This workflow image is designed to help with Giotto Installation. 
		Users should follow the installation process by answering Yes or No to the following quetions. 
		There are three potential paths aimed to guide users through the installation process (see the diagram below for an overview). 

		**Part 1: R Giotto Requirements** of Giotto Installation is centered around preparing your R Environment 
		
		**Part 2: Python Giotto Requirements** of Giotto Installation is centered around preparing your Python Environment 

 
		.. dropdown:: Workflow Diagram 
		
			.. image:: images/GiottoInstallationWorkflow.png
				:width: 600
				:alt: Image of Giotto Installation Workflow
				:align: center

Part 1: R Giotto Requirements
============================== 

.. tab-set::

	.. tab-item:: 1.1: R Package Installation

		:octicon:`question` **Have you ever installed an R package that is in development?**

		.. dropdown:: Yes 
 
			:octicon:`arrow-right` Move on to the next step: Giotto Installation.
		
		.. dropdown:: No

			If this is the first time you build and install an R package you can follow `this link <https://support.rstudio.com/hc/en-us/articles/200486498-Package-Development-Prerequisites>`_, which has simple installation instructions for Windows, Mac OSX and Linux.
			To specifically install the command-line tools of Xcode for Mac OSX you might also need to run this line **in terminal**:

			.. code-block:: 

  				xcode-select -- install 

			:octicon:`arrow-right` Move on to the next step: Giotto Installation  


	.. tab-item:: 1.2: Giotto Installation 
		
		:octicon:`question` **Have you installed Giotto before?**

		.. dropdown:: Yes 

			Run the following code to load giotto intto your R workspace. 

			.. code-block::

				library(Giotto)

			:octicon:`arrow-right` Move on to the next step: Python Package Installation  

		.. dropdown:: No


			If you have installed an R Package before but this is your first time installting Giotto pleae follow the instructions below.
			Giotto can be installed within 1-5 mins.

			**This version requires C Compilation**

			.. code-block::

				library(devtools) # If not installed: install.packages('devtools')
				library(remotes)  #If not installed: install.packages('remotes')
				remotes::install_github("drieslab/Giotto") 

			.. warning:: 	
				If you are having compilation (gfortran) problems check goftran.

			**This version does not require C Compliation**	

			.. code-block::

				remotes::install_github("drieslab/Giotto@cless") 

			Next, run the following code to load giotto intto your R workspace. 

			.. code-block::

				library(Giotto)

			Then, :octicon:`arrow-right` move on to the next step: Python Package Installation  

.. _part2_python_giotto_requirements:

Part 2: Python Giotto Requirements 
=======================================

.. tab-set:: 

	.. tab-item:: 2.1: Python Package Installation 

		:octicon:`question` **Have you ever installed a Python Package?**

		.. dropdown:: Yes

			:octicon:`arrow-right` Move on to the next step: Giotto-Specific Python Packages 

		.. dropdown:: No 

			Information on how to install a Python package can be found `here`_.
			Once you have read through the information :octicon:`arrow-right` proceeed to the next step: Giotto-Specific Python Packages. 


	.. tab-item:: 2.2: Giotto-Specific Python Packages

		:octicon:`question` **Have you installed the python modules necessary for Giotto to run?**
		
		.. dropdown:: Required Modules 

			To perform all potential steps and analysis in the Giotto spatial toolbox the user needs to have a number of python modules installed. 
			To make this process as flexible and easy as possible two different strategies can be used

			* pandas
			* python-igraph (igraph)
			* networkx
			* leidenalg
			* python-louvain (community)
			* smfishHmrf
			* python.app (**OSX only**)
			* scikit-learn

			:octicon:`alert` These are necessary to run all available analyses, but can be installed automatically (see **2.2A Automatic Installation**) or manually (see **2.2B Manual Installation**) explained in detail below (Reponse "No").

		.. dropdown:: Yes 

			:octicon:`arrow-right` Move on to the next step: Python Path Specification 

		.. dropdown:: No
			:open:

			There are two methods through which users may install the required python modules ('Automatic Installation' vs. 'Manual Installation')
			
			.. dropdown:: 2.2A: Automatic Installation 

				The python modules will be installed automatically in a miniconda environment when installing Giotto. However, it will ask you whether you want to install them and you can opt out and select your preferred python path. In that case you need to do a manual installation of the python modules.
				To perform all potential steps and analysis in the Giotto spatial toolbox the user needs to have a number of python modules installed. Installation of the Giotto Environment allows users to install the required Python environment via MiniConda without specifying their python path. 
				
				With this option, the user can just install a Giotto python environment using r-miniconda. This is done *after* installation of the Giotto package via R (see Part 1: Giotto Installation for more information).  

				First, load the Giotto Package then proceed to environment installation and/or removal. 

				.. code-block::

					library(Giotto)

				.. dropdown:: Install Giotto Environment 

					.. code-block::

						installGiottoEnvironmnt()

				.. dropdown:: Re-Install the Giotto environment

					.. code-block::

						installGiottoEnvironment(force_environment = TRUE)

				.. dropdown:: Re-install mini-conda and environment

					.. code-block::
						
						installGiottoEnvironment(force_miniconda = TRUE)
					
				.. dropdown:: Remove Giotto Environment

					.. code-block::

						removeGiottoEnvironment()

				.. note::
					With the automatic installation option, the user **DOES NOT** have to specify a python path. 
				

			.. dropdown:: 2.2B: Manual Installation

				There are two methods thorugh which users can install the necessary Python modules manually with **(1) Pip in Python3** or **(2) Conda**.

				.. warning:: 
					With either one of the **Manual Installation** options (**Install with Pip in Python3** or **Install with Conda**) users will have to provide the python path to :ref:`createGiottoInstructions <createGiottoInstructions>`. 
					If this is not done, Giotto **will not** be able to use the installed python modules.
					
				*Note:* If pip install does not work, try installing the modules within a `conda environment <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands>`_. For more inforomation on vitual environments with conda visit this `page <https://heartbeat.fritz.ai/creating-python-virtual-environments-with-conda-why-and-how-180ebd02d1db>`_.

				.. dropdown:: **1. Install with Pip in Python3**

					*For OSX, Windows, or Linux:* 

					.. code-block:: 

						pip3 install pandas python-igraph networkx python-louvain leidenalg scikit-learn smfishHmrf

				.. dropdown:: **2. Install with Conda**

					1. Create yaml file (e.g. environment.yml) with the following information:

						.. code-block:: 

							name: giotto_env
							channels:
							— defaults
							dependencies:
							— pip=3.4
							— pandas
							— networkx
							- python-igraph
							- leidenalg
							- python-louvain
							- python.app (!!only for OSX!!)
							- scikit-learn
							prefix: /Users/your_username/anaconda3/envs/giotto_env

					2. Create Conda Environment Based on yaml File: 

						.. code-block:: 

							conda env create -f environment.yml
					
					3. Use the path to this environment when you create the Giotto instructions or Giotto object

						.. dropdown:: For OSX
						
							.. code-block::
							
								/Users/your_username/anaconda3/envs/giotto_env/bin/pythonw


						.. dropdown:: For Windows
						
							.. code-block:: 
							
								/Users/your_username/anaconda3/envs/giotto_env/python.exe


						.. dropdown:: For Linux
						
							.. code-block:: 
							
								/Users/your_username/anaconda3/envs/giotto_env/bin/python


			.. warning:: 
				With either one of the Manual Installation options users **must** specify their Python path. 

			Please :octicon:`arrow-right` move on to the next step: 2.3 Python Path Specification 

	.. tab-item:: 2.3 Python Path Specification 

		:octicon:`question` **Have you specified your python path in R?**

		.. note::
			
			With the Manual Installation option, once the user has installed all of the necessary modules, via either of the methods (e.g. Conda), the path to their python environment can be provided as an instruction in R.

		.. dropdown:: Yes

			You can now start using Giotto :octicon:`verified` 

		.. dropdown:: No 

				With the Manual Installation option, once the user has installed all of the necessary modules, via either of the methods above (e.g. Conda), the path to their python environment can be provided as an instruction in R.

				.. code-block::
						
						# Within R
					
						library(Giotto)
						my_instructions = createGiottoInstructions(python_path = 'your/python/path')
						my_giotto_object = createGiottoObject(
							raw_exprs = '...',
							spatial_locs = '...', 
							instructions = my_instructions)

				You can now start using Giotto :octicon:`verified` 

.. _here: https://packaging.python.org/en/latest/tutorials/installing-packages/


.. tip:: 
	
	Check out the :ref:`Examples Section <datasets>` to get a better understanding of the Giotto workflow.


.. _howtolabel:

Giotto Analyzer and Viewer interaction [*work in progress*]
===========================================================

* How to switch between Giotto Analyzer and Viewer?

Tips and Tricks
============================================================

* :ref:`Different ways of subsetting Giotto results? <ways-of-subsetting>`
* :ref:`How to create global instructions and show or save your created plots? <global-instructions-and-save-plots>`
* :ref:`Different ways to visualize your spatial data? <visualize-data>`
* :ref:`How to test and store multiple parameters or analyses? <test-and-store>`
* :ref:`Visualize spatial data with voronoi plots <voronoi-plots>`
* :ref:`Working with the Giotto class <giotto-class>`
* :ref:`Adding and Working with Images in Giotto <working-with-giotto-images>`


.. dropdown:: :doc:`Giotto Workflow Analyses Guided Steps</giottoworkflowanalyses>`
	:animate: fade-in 
	
	0. :ref:`*Optional* Install a Giotto Environment <install_a_giotto_environment>` 
	1. :ref:`Create a Giotto Object <create-a-giotto-object>`
	#. :ref:`Process and Filter a Giotto Object <process-and-filter-a-giotto-object>` 
	#. :ref:`Dimension Reduction <dimension-reduction>`
	#. :ref:`Cluster cells or spots <cluster_cells-or-spots>`
	#. :ref:`Identify differentially expressed genes <identify-differentially-expressed-genes>`
	#. :ref:`Annotate clusters <annotate-clusters>`
	#. :ref:`Cell-type enrichment or deconvolution per spot <cell-type-enrichment-or-deconvolution-per-spot>`
	#. :ref:`Create a Spatial grid or Network <spatial-grid-or-network>`
	#. :ref:`Find genes with a spatially coherent gene expression pattern <spatially-coherent-gene-expression-pattern>`
	#. :ref:`Identify genes that are spatially co-expressed <spatially-coexpressed-genes>`
	#. :ref:`Explore spatial domains with HMRF <spatial-domains-with-HMRF>`
	#. :ref:`Calculate spatial cell-cell interaction enrichment <calculate-spatial-cell-cell-interaction>`
	#. :ref:`Find cell-cell interaction changed genes (ICG) <find-cell-cell-interactions-changed-genes>`
	#. :ref:`Identify enriched or depleted ligand-receptor interactions in hetero and homo-typic cell interactions <enriched-or-depleted-ligand-receptor-interactions>`
	#. :ref:`Export Giotto results to use in Giotto viewer <giotto-viewer-export>`


.. dropdown:: :ref:`HowTos <howtosection>`
	:animate: fade-in 
	
	* :ref:`Different ways of subsetting Giotto results? <ways-of-subsetting>`
	* :ref:`How to create global instructions and show or save your created plots? <global-instructions-and-save-plots>`
	* :ref:`Different ways to visualize your spatial data? <visualize-data>`
	* :ref:`How to test and store multiple parameters or analyses? <test-and-store>`
	* :ref:`Visualize spatial data with voronoi plots <voronoi-plots>`
	* :ref:`Working with the Giotto class <giotto-class>`
	* :ref:`Adding and Working with Images in Giotto <working-with-giotto-images>`


.. dropdown:: Giotto Analyzer and Viewer interaction [*work in progress*] :octicon:`alert`
	:color: warning

	* How to switch between Giotto Analyzer and Viewer?


.. seealso:: 
	:ref:`FAQs <faqs>` for more help.
