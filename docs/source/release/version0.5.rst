=============
Release 0.5.0
=============

Statsmodels 0.5 is a large and very exciting release that brings together a year of work done by 38 authors, including over 2000 commits. It contains many new features and a large amount of bug fixes detailed below.

See the :ref:`list of fixed issues <issues_list_05>` for specific closed issues.

The following major new features appear in this version.

Support for Model Formulas via Patsy
====================================

Statsmodels now supports fitting models with a formula. This functionality is provided by `patsy <https://patsy.readthedocs.org/en/latest/>`_. Patsy is now a dependency for statsmodels. Models can be individually imported from the ``statsmodels.formula.api`` namespace or you can import them all as::

    import statsmodels.formula.api as smf

Alternatively, each model in the usual ``statsmodels.api`` namespace has a ``from_formula`` classmethod that will create a model using a formula. Formulas are also available for specifying linear hypothesis tests using the ``t_test`` and ``f_test`` methods after model fitting. A typical workflow can now look something like this.

.. code-block:: python

    import numpy as np
    import pandas as pd
    import statsmodels.formula.api as smf

    url = 'https://raw.githubusercontent.com/vincentarelbundock/Rdatasets/csv/HistData/Guerry.csv'
    data = pd.read_csv(url)

    # Fit regression model (using the natural log of one of the regressors)
    results = smf.ols('Lottery ~ Literacy + np.log(Pop1831)', data=data).fit()

See :ref:`here for some more documentation of using formulas in statsmodels <formula_examples>`

Empirical Likelihood (Google Summer of Code 2012 project)
---------------------------------------------------------

Empirical Likelihood-Based Inference for moments of univariate and multivariate variables is available as well as EL-based ANOVA tests. EL-based linear regression, including the regression through the origin model. In addition, the accelerated failure time model for inference on a linear regression model with a randomly right censored endogenous variable is available.

Analysis of Variance (ANOVA) Modeling
-------------------------------------

Support for ANOVA is now available including type I, II, and III sums of squares. See :ref:`anova`.

.. currentmodule:: statsmodels.nonparametric

Multivariate Kernel Density Estimators (GSoC 2012 project)
----------------------------------------------------------

Kernel density estimation has been extended to handle multivariate estimation as well via product kernels. It is available as :class:`sm.nonparametric.KDEMultivariate <kernel_density.KDEMultivariate>`. It supports least squares and maximum likelihood cross-validation for bandwidth estimation, as well as mixed continuous, ordered, and unordered categorical data. Conditional density estimation is also available via :class:`sm.nonparametric.KDEMUltivariateConditional <kernel_density.KDEMultivariateConditional>`.

Nonparameteric Regression (GSoC 2012 project)
---------------------------------------------
Kernel regression models are now available via :class:`sm.nonparametric.KernelReg <kernel_regression.KernelReg>`. It is based on the product kernel mentioned above, so it also has the same set of features including support for cross-validation as well as support for estimation mixed continuous and categorical variables. Censored kernel regression is also provided by `kernel_regression.KernelCensoredReg`.

Quantile Regression Model
-------------------------

.. currentmodule:: statsmodels.regression.quantile_regression

Quantile regression is supported via the :class:`sm.QuantReg <QuantReg>` class. Kernel and bandwidth selection options are available for estimating the asymptotic covariance matrix using a kernel density estimator.

Negative Binomial Regression Model
----------------------------------

.. currentmodule:: statsmodels.discrete.discrete_model

It is now possible to fit negative binomial models for count data via maximum-likelihood using the :class:`sm.NegativeBinomial <NegativeBinomial>` class. ``NB1``, ``NB2``, and ``geometric`` variance specifications are available.

l1-penalized Discrete Choice Models
-----------------------------------

A new optimization method has been added to the discrete models, which includes Logit, Probit, MNLogit and Poisson, that makes it possible to estimate the models with an l1, linear, penalization. This shrinks parameters towards zero and can set parameters that are not very different from zero to zero. This is especially useful if there are a large number of explanatory variables and a large associated number of parameters. `CVXOPT <http://cvxopt.org/>`_ is now an optional dependency that can be used for fitting these models.

New and Improved Graphics
-------------------------

.. currentmodule:: statsmodels.graphics

* **ProbPlot**: A new `ProbPlot` object has been added to provide a simple interface to create P-P, Q-Q, and probability plots with options to fit a distribution and show various reference lines. In the case of Q-Q and P-P plots, two different samples can be compared with the `other` keyword argument. :func:`sm.graphics.ProbPlot <gofplots.ProbPlot>`

.. code-block:: python
   
   import numpy as np
   import statsmodels.api as sm
   x = np.random.normal(loc=1.12, scale=0.25, size=37)
   y = np.random.normal(loc=0.75, scale=0.45, size=37)
   ppx = sm.ProbPlot(x)
   ppy =  sm.ProbPlot(y)
   fig1 = ppx.qqplot()
   fig2 = ppx.qqplot(other=ppy)

* **Mosaic Plot**: Create a mosaic plot from a contingency table. This allows you to visualize multivariate categorical data in a rigorous and informative way. Available with :func:`sm.graphics.mosaic <mosaicplot.mosaic>`.

* **Interaction Plot**: Interaction plots now handle categorical factors as well as other improviments. :func:`sm.graphics.interaction_plot <factorplots.interaction_plot>`.

* **Regression Plots**: The regression plots have been refactored and improved. They can now handle pandas objects and regression results instances appropriately. See :func:`sm.graphics.plot_fit <regressionplots.plot_fit>`, :func:`sm.graphics.plot_regress_exog <regressionplots.plot_regress_exog>`, :func:`sm.graphics.plot_partregress <regressionplots.plot_partregress>`, :func:`sm.graphics.plot_ccpr   <regressionplots.plot_ccpr>`, :func:`sm.graphics.abline_plot <regressionplots.abline_plot>`, :func:`sm.graphics.influence_plot <regressionplots.influence_plot>`, and :func:`sm.graphics.plot_leverage_resid2 <regressionplots.plot_leverage_resid2>`.

.. currentmodule:: statsmodels.stats.power

Power and Sample Size Calculations
----------------------------------

The power module (``statsmodel.stats.power``) currently implements power and sample size calculations for the t-tests (:class:`sm.stats.TTestPower <TTestPower>`, :class:`sm.stats.TTestIndPower <TTestIndPower>`), normal based test (:class:`sm.stats.NormIndPower <NormIndPower>`), F-tests (:class:`sm.stats.FTestPower <FTestPower>`, `:class:sm.stats.FTestAnovaPower <FTestAnovaPower>`) and Chisquare goodness of fit (:class:`sm.stats.GofChisquarePower <GofChisquarePower>`) test. The implementation is class based, but the module also provides three shortcut functions, :func:`sm.stats.tt_solve_power <tt_solve_power>`, :func:`sm.stats.tt_ind_solve_power <tt_ind_solve_power>` and :func:`sm.stats.zt_ind_solve_power <zt_ind_solve_power>` to solve for any one of the parameters of the power equations. See this `blog post <http://jpktd.blogspot.fr/2013/03/statistical-power-in-statsmodels.html>`_ for a more in-depth description of the additions.


Other important new features
----------------------------
* **IPython notebook examples**: Many of our examples have been converted or added as IPython notebooks now. They are available `here <https://www.statsmodels.org/devel/examples/index.html#notebook-examples>`_.

* **Improved marginal effects for discrete choice models**: Expanded options for obtaining marginal effects after the estimation of nonlinear discrete choice models are available. See :py:meth:`get_margeff <statsmodels.discrete.discrete_model.DiscreteResuls.get_margeff>`.

* **OLS influence outlier measures**: After the estimation of a model with OLS, the common set of influence and outlier measures and a outlier test are now available attached as methods ``get_influnce`` and ``outlier_test`` to the Results instance. See :py:class:`OLSInfluence <statsmodels.stats.outliers_influence.OLSInfluence>` and :func:`outlier_test <statsmodels.stats.outliers_influence.outlier_test>`.

* **New datasets**: New :ref:`datasets <datasets>` are available for examples.

* **Access to R datasets**: We now have access to many of the same datasets available to R users through the `Rdatasets project <https://vincentarelbundock.github.io/Rdatasets/>`_. You can access these using the :func:`sm.datasets.get_rdataset <statsmodels.datasets.get_rdataset>` function. This function also includes caching of these datasets.

* **Improved numerical differentiation tools**: Numerical differentiation routines have been greatly improved and expanded to cover all the routines discussed in::

    Ridout, M.S. (2009) Statistical applications of the complex-step method
        of numerical differentiation. The American Statistician, 63, 66-74

  See the :ref:`sm.tools.numdiff <numdiff>` module.

* **Consistent constant handling across models**: Result statistics no longer rely on the assumption that a constant is present in the model.

* **Missing value handling across models**: Users can now control what models do in the presence of missing values via the ``missing`` keyword available in the instantiation of every model. The options are ``'none'``, ``'drop'``, and ``'raise'``. The default is ``'none'``, which does no missing value checks. To drop missing values use ``'drop'``. And ``'raise'`` will raise an error in the presence of any missing data.

.. currentmodule:: statsmodels.iolib

* **Ability to write Stata datasets**: Added the ability to write Stata ``.dta`` files. See :class:`sm.iolib.StataWriter <foreign.StataWriter>`.

.. currentmodule:: statsmodels.tsa.arima_model

* **ARIMA modeling**: Statsmodels now has support for fitting Autoregressive Integrated Moving Average (ARIMA) models. See :class:`ARIMA` and :class:`ARIMAResults` for more information.

* **Support for dynamic prediction in AR(I)MA models**: It is now possible to obtain dynamic in-sample forecast values in :class:`ARMA` and :class:`ARIMA` models.

* **Improved Pandas integration**: Statsmodels now supports all frequencies available in pandas for time-series modeling. These are used for intelligent dates handling for prediction. These features are available, if you pass a pandas Series or DataFrame with a DatetimeIndex to a time-series model.

.. currentmodule:: statsmodels

* **New statistical hypothesis tests**: Added statistics for calculating interrater agreement including Cohen's kappa and Fleiss' kappa (See :ref:`interrater`), statistics and hypothesis tests for proportions (See :ref:`proportion stats <proportion_stats>`), Tukey HSD (with plot) was added as an enhancement to the multiple comparison tests (:class:`sm.stats.multicomp.MultiComparison <sandbox.stats.multicomp.MultiComparison>`, :func:`sm.stats.multicomp.pairwise_tukeyhsd <stats.multicomp.pairwise_tukeyhsd>`). Weighted statistics and t tests were enhanced with new options. Tests of equivalence for one sample and two independent or paired samples were added based on t tests and z tests (See :ref:`tost`).  


Major Bugs fixed
----------------

* Post-estimation statistics for weighted least squares that depended on the centered total sum of squares were not correct. These are now correct and tested. See :ghissue:`501`.

* Regression through the origin models now correctly use uncentered total sum of squares in post-estimation statistics. This affected the :math:`R^2` value in linear models without a constant. See :ghissue:`27`.

Backwards incompatible changes and deprecations
-----------------------------------------------

* Cython code is now non-optional. You will need a C compiler to build from source. If building from github and not a source release, you will also need Cython installed. See the :ref:`installation documentation <install>`.

* The ``q_matrix`` keyword to `t_test` and `f_test` for linear models is deprecated. You can now specify linear hypotheses using formulas.

.. currentmodule:: statsmodels.tsa

* The ``conf_int`` keyword to :func:`sm.tsa.acf <stattools.acf>` is deprecated.

* The ``names`` argument is deprecated in :class:`sm.tsa.VAR <vector_ar.var_model.VAR>` and `sm.tsa.SVAR <vector_ar.svar_model.SVAR>`. This is now automatically detected and handled.

.. currentmodule:: statsmodels.tsa

* The ``order`` keyword to :py:meth:`sm.tsa.ARMA.fit <ARMA.fit>` is deprecated. It is now passed in during model instantiation.

.. currentmodule:: statsmodels.distributions

* The empirical distribution function (:class:`sm.distributions.ECDF <ECDF>`) and supporting functions have been moved to ``statsmodels.distributions``. Their old paths have been deprecated.

* The ``margeff`` method of the discrete choice models has been deprecated. Use ``get_margeff`` instead. See above. Also, the vague ``resid`` attribute of the discrete choice models has been deprecated in favor of the more descriptive ``resid_dev`` to indicate that they are deviance residuals.

.. currentmodule:: statsmodels.nonparametric.kde

* The class ``KDE`` has been deprecated and renamed to :class:`KDEUnivariate` to distinguish it from the new ``KDEMultivariate``. See above.

Development summary and credits
-------------------------------

The previous version (statsmodels 0.4.3) was released on July 2, 2012. Since then we have closed a total of 380 issues, 172 pull requests and 208 regular issues. The :ref:`detailed list<issues_list_05>` can be viewed.

This release is a result of the work of the following 38 authors who contributed total of 2032 commits. If for any reason, we've failed to list your name in the below, please contact us:

* Ana Martinez Pardo <anamartinezpardo-at-gmail.com>
* anov <novikova.go.zoom-at-gmail.com>
* avishaylivne <avishay.livne-at-gmail.com>
* Bruno Rodrigues <rodrigues.bruno-at-aquitania.org>
* Carl Vogel <carljv-at-gmail.com>
* Chad Fulton <chad-at-chadfulton.com>
* Christian Prinoth <christian-at-prinoth.name>
* Daniel B. Smith <neuromathdan-at-gmail.com>
* dengemann <denis.engemann-at-gmail.com>
* Dieter Vandenbussche <dvandenbussche-at-axioma.com>
* Dougal Sutherland <dougal-at-gmail.com>
* Enrico Giampieri <enrico.giampieri-at-unibo.it>
* evelynmitchell <efm-github-at-linsomniac.com>
* George Panterov <econgpanterov-at-gmail.com>
* Grayson <graysonbadgley-at-gmail.com>
* Jan Schulz <jasc-at-gmx.net>
* Josef Perktold <josef.pktd-at-gmail.com>
* Jeff Reback <jeff-at-reback.net>
* Justin Grana <jg3705a-at-student.american.edu>
* langmore <ianlangmore-at-gmail.com>
* Matthew Brett <matthew.brett-at-gmail.com>
* Nathaniel J. Smith <njs-at-pobox.com>
* otterb <itoi-at-live.com>
* padarn <padarn-at-wilsonp.anu.edu.au>
* Paul Hobson <pmhobson-at-gmail.com>
* Pietro Battiston <me-at-pietrobattiston.it>
* Ralf Gommers <ralf.gommers-at-googlemail.com>
* Richard T. Guy <richardtguy84-at-gmail.com>
* Robert Cimrman <cimrman3-at-ntc.zcu.cz>
* Skipper Seabold <jsseabold-at-gmail.com>
* Thomas Haslwanter <thomas.haslwanter-at-fh-linz.at>
* timmie <timmichelsen-at-gmx-topmail.de>
* Tom Augspurger <thomas-augspurger-at-uiowa.edu>
* Trent Hauck <trent.hauck-at-gmail.com>
* tylerhartley <tyleha-at-gmail.com>
* Vincent Arel-Bundock <varel-at-umich.edu>
* VirgileFritsch <virgile.fritsch-at-gmail.com>
* Zhenya <evgeni-at-burovski.me>

.. note:: 

   Obtained by running ``git log v0.4.3..HEAD --format='* %aN <%aE>' | sed 's/@/\-at\-/' | sed 's/<>//' | sort -u``.

.. _issues_list_05:

Issues closed in the 0.5.0 development cycle
============================================

Issued closed in 0.5.0
-----------------------

GitHub stats for release 0.5.0 (07/02/2012/ - 08/14/2013/).

We closed a total of 380 issues, 172 pull requests and 208 regular issues. This is the full list (generated with the script  :file:`tools/github_stats.py`):

This list is automatically generated, and may be incomplete:

Pull Requests (172):

* :ghpull:`1015`: DOC: Bump version. Remove done tasks.
* :ghpull:`1010`: DOC/RLS: Update release notes workflow. Help Needed!
* :ghpull:`1014`: DOC: nbgenerate does not like the comment at end of line.
* :ghpull:`1012`: DOC: Add link to notebook and crosslink ref. Closes #924.
* :ghpull:`997`: misc, tests, diagnostic
* :ghpull:`1009`: MAINT: Add .mailmap file.
* :ghpull:`817`: Add 3 new unit tests for arima_process
* :ghpull:`1001`: BUG include_package_data for install closes #907
* :ghpull:`1005`: GITHUB: Contributing guidlines
* :ghpull:`1007`: Cleanup docs for release
* :ghpull:`1003`: BUG: Workaround for bug in sphinx 1.1.3. See #1002.
* :ghpull:`1004`: DOC: Update maintainer notes with branching instructions.
* :ghpull:`1000`: BUG: Support pandas 0.8.0.
* :ghpull:`996`: BUG: Handle combo of pandas 0.8.0 and dateutils 1.5.0
* :ghpull:`995`: ENH: Print dateutil version.
* :ghpull:`994`: ENH: Fail gracefully for version not found.
* :ghpull:`993`: More conservative error catching in TimeSeriesModel
* :ghpull:`992`: Misc fixes 12: adjustments to unit test
* :ghpull:`985`: MAINT: Print versions script.
* :ghpull:`986`: ENH: Prefer to_offset to get_offset. Closes #964.
* :ghpull:`984`: COMPAT: Pandas 0.8.1 compatibility. Closes #983.
* :ghpull:`982`: Misc fixes 11
* :ghpull:`978`: TST: generic mle pareto disable bsejac tests with estimated loc
* :ghpull:`977`: BUG python 3.3 fix for numpy str TypeError, see #633
* :ghpull:`975`: Misc fixes 10 numdiff
* :ghpull:`970`: BUG: array too long, raises exception with newer numpy closes #967
* :ghpull:`965`: Vincent summary2 rebased
* :ghpull:`933`: Update and improve GenericlikelihoodModel and miscmodels
* :ghpull:`950`: BUG/REF mcnemar fix exact pvalue, allow table as input
* :ghpull:`951`: Pylint emplike formula genmod
* :ghpull:`956`: Fix a docstring in KDEMultivariateConditional.
* :ghpull:`949`: BUG fix lowess sort when nans closes #946
* :ghpull:`932`: ENH: support basinhopping solver in LikelihoodModel.fit()
* :ghpull:`927`: DOC: clearer minimal example
* :ghpull:`919`: Ols summary crash
* :ghpull:`918`: Fixes10 emplike lowess
* :ghpull:`909`: Bugs in GLM pvalues, more tests, pylint
* :ghpull:`906`: ENH: No fmax with Windows SDK so define inline.
* :ghpull:`905`: MAINT more fixes
* :ghpull:`898`: Misc fixes 7
* :ghpull:`896`: Quantreg rebase2
* :ghpull:`895`: Fixes issue #832
* :ghpull:`893`: ENH: Remove unneeded restriction on low. Closes #867.
* :ghpull:`894`: MAINT: Remove broken function. Keep deprecation. Closes #781.
* :ghpull:`856`: Carljv improved lowess rebased2
* :ghpull:`884`: Pyflakes cleanup
* :ghpull:`887`: BUG: Fix kde caching
* :ghpull:`883`: Fixed pyflakes issue in discrete module
* :ghpull:`882`: Update predstd.py
* :ghpull:`871`: Update of sandbox doc
* :ghpull:`631`: WIP: Correlation positive semi definite
* :ghpull:`857`: BLD: apt get dependencies from Neurodebian, whitespace cleanup
* :ghpull:`855`: AnaMP issue 783 mixture rvs tests rebased
* :ghpull:`854`: Enrico multinear rebased
* :ghpull:`849`: Tyler tukeyhsd rebased
* :ghpull:`848`: BLD TravisCI use python-dateutil package
* :ghpull:`784`: Misc07 cleanup multipletesting and proportions
* :ghpull:`841`: ENH: Add load function to main API. Closes #840.
* :ghpull:`820`: Ensure that tuples are not considered as data, not as data containers
* :ghpull:`822`: DOC: Update for Cython changes.
* :ghpull:`765`: Fix build issues
* :ghpull:`800`: Automatically generate output from notebooks
* :ghpull:`802`: BUG: Use two- not one-sided t-test in t_test. Closes #740.
* :ghpull:`806`: ENH: Import formula.api in statsmodels.api namespace.
* :ghpull:`803`: ENH: Fix arima error message for bad start_params
* :ghpull:`801`: DOC: Fix ANOVA section titles
* :ghpull:`795`: Negative Binomial Rebased
* :ghpull:`787`: Origintests
* :ghpull:`794`: ENH: Allow pandas-in/pandas-out in tsa.filters
* :ghpull:`791`: Github stats for release notes
* :ghpull:`779`: added np.asarray call to durbin_watson in stattools
* :ghpull:`772`: Anova docs
* :ghpull:`776`: BUG: Fix dates_from_range with length. Closes #775.
* :ghpull:`774`: BUG: Attach prediction start date in AR. Closes #773.
* :ghpull:`767`: MAINT: Remove use of deprecated from examples and docs.
* :ghpull:`762`: ENH: Add new residuals to wrapper
* :ghpull:`754`: Fix arima predict
* :ghpull:`760`: ENH: Adjust for k_trend in information criteria. Closes #324.
* :ghpull:`761`: ENH: Fixes and tests sign_test. Closes #642.
* :ghpull:`759`: Fix 236
* :ghpull:`758`: DOC: Update VAR docs. Closes #537.
* :ghpull:`752`: Discrete cleanup
* :ghpull:`750`: VAR with 1d array
* :ghpull:`748`: Remove reference to new_t_test and new_f_test.
* :ghpull:`739`: DOC: Remove outdated note in docstring
* :ghpull:`732`: BLD: Check for patsy dependency at build time + docs
* :ghpull:`731`: Handle wrapped
* :ghpull:`730`: Fix opt fulloutput
* :ghpull:`729`: Get rid of warnings in docs build
* :ghpull:`698`: update url for hsb2 dataset
* :ghpull:`727`: DOC: Fix indent and add missing params to linear models. Closes #709.
* :ghpull:`726`: CLN: Remove unused method. Closes #694
* :ghpull:`725`: BUG: Should call anova_single. Closes #702.
* :ghpull:`723`: Rootfinding for Power
* :ghpull:`722`: Handle pandas.Series with names in make_lags
* :ghpull:`714`: Fix 712
* :ghpull:`668`: Allow for any pandas frequency to be used in TimeSeriesModel.
* :ghpull:`711`: Misc06 - bug fixes
* :ghpull:`708`: BUG: Fix one regressor case for conf_int. Closes #706.
* :ghpull:`700`: Bugs rebased
* :ghpull:`680`: BUG: Swap arguments in fftconvolve for scipy >= 0.12.0
* :ghpull:`640`: Misc fixes 05
* :ghpull:`663`: a typo in runs.py doc string for mcnemar test
* :ghpull:`652`: WIP: fixing pyflakes / pep8, trying to improve readability
* :ghpull:`619`: DOC: intro to formulas
* :ghpull:`648`: BF: Make RLM stick to Huber's description
* :ghpull:`649`: Bug Fix
* :ghpull:`637`: Pyflakes cleanup
* :ghpull:`634`: VAR DOC typo
* :ghpull:`623`: Slowtests
* :ghpull:`621`: MAINT: in setup.py, only catch ImportError for pandas.
* :ghpull:`590`: Cleanup test output
* :ghpull:`591`: Interrater agreement and reliability measures
* :ghpull:`618`: Docs fix the main warnings and errors during sphinx build
* :ghpull:`610`: nonparametric examples and some fixes
* :ghpull:`578`: Fix 577
* :ghpull:`575`: MNT: Remove deprecated scikits namespace
* :ghpull:`499`: WIP: Handle constant
* :ghpull:`567`: Remove deprecated
* :ghpull:`571`: Dataset docs
* :ghpull:`561`: Grab rdatasets
* :ghpull:`570`: DOC: Fixed links to Rdatasets
* :ghpull:`524`: DOC: Clean up discrete model documentation.
* :ghpull:`506`: ENH: Re-use effects if model fit with QR
* :ghpull:`556`: WIP:  L1 doc fix
* :ghpull:`564`: TST: Use native integer to avoid issues in dtype asserts
* :ghpull:`543`: Travis CI using M.Brett nipy hack
* :ghpull:`558`: Plot cleanup
* :ghpull:`541`: Replace pandas DataMatrix with DataFrame
* :ghpull:`534`: Stata test fixes
* :ghpull:`532`: Compat 323
* :ghpull:`531`: DOC: Add ECDF to distributions docs
* :ghpull:`526`: ENH: Add class to write Stata binary dta files
* :ghpull:`521`: DOC: Add abline plot to docs
* :ghpull:`518`: Small fixes: interaction_plot
* :ghpull:`508`: ENH: Avoid taking cholesky decomposition of diagonal matrix
* :ghpull:`509`: DOC: Add ARIMA to docs
* :ghpull:`510`: DOC: realdpi is disposable personal income. Closes #394.
* :ghpull:`507`: ENH: Protect numdifftools import. Closes #45
* :ghpull:`504`: Fix weights
* :ghpull:`498`: DOC: Add patys requirement to install docs
* :ghpull:`491`: Make _data a public attribute.
* :ghpull:`494`: DOC: Fix pandas links
* :ghpull:`492`: added intersphinx for pandas
* :ghpull:`422`: Handle missing data
* :ghpull:`485`: ENH: Improve error message for pandas objects without dates in index
* :ghpull:`428`: Remove other data
* :ghpull:`483`: Arima predict bug
* :ghpull:`482`: TST: Do array-array comparison when using numpy.testing
* :ghpull:`471`: Formula rename df -> data
* :ghpull:`473`: Vincent docs tweak rebased
* :ghpull:`468`: Docs 050
* :ghpull:`462`: El aft rebased
* :ghpull:`461`: TST: numpy 1.5.1 compatibility
* :ghpull:`460`: Emplike desc reg rebase
* :ghpull:`410`: Discrete model marginal effects
* :ghpull:`417`: Numdiff cleanup
* :ghpull:`398`: Improved plot_corr and plot_corr_grid functions.
* :ghpull:`401`: BUG: Finish refactoring margeff for dummy. Closes #399.
* :ghpull:`400`: MAINT: remove lowess.py, which was kept in 0.4.x for backwards compatibi...
* :ghpull:`371`: BF+TEST: fixes, checks and tests for isestimable
* :ghpull:`351`: ENH: Copy diagonal before write for upcoming numpy changes
* :ghpull:`384`: REF: Move mixture_rvs out of sandbox.
* :ghpull:`368`: ENH: Add polished version of acf/pacf plots with confidence intervals
* :ghpull:`378`: Infer freq
* :ghpull:`374`: ENH: Add Fair's extramarital affair dataset. From tobit-model branch.
* :ghpull:`358`: ENH: Add method to OLSResults for outlier detection
* :ghpull:`369`: ENH: allow predict to pass through patsy for transforms
* :ghpull:`352`: Formula integration rebased
* :ghpull:`360`: REF: Deprecate order in fit and move to ARMA init
* :ghpull:`366`: Version fixes
* :ghpull:`359`: DOC: Fix sphinx warnings

Issues (208):

* :ghissue:`1036`: Series no longer inherits from ndarray
* :ghissue:`1038`: DataFrame with integer names not handled in ARIMA
* :ghissue:`1028`: Test fail with windows and Anaconda - Low priority
* :ghissue:`676`: acorr_breush_godfrey  undefined nlags
* :ghissue:`922`: lowess returns inconsistent with option
* :ghissue:`425`: no bse in robust with norm=TrimmedMean
* :ghissue:`1025`: add_constant incorrectly detects constant column
* :ghissue:`533`: py3 compatibility ``pandas.read_csv(urlopen(...))``
* :ghissue:`662`: doc: install instruction: explicit about removing scikits.statsmodels
* :ghissue:`910`: test failure Ubuntu TestARMLEConstant.test_dynamic_predict
* :ghissue:`80`: t_model: f_test, t_test don't work
* :ghissue:`432`: GenericLikelihoodModel change default for score and hessian
* :ghissue:`454`: BUG/ENH: HuberScale instance is not used, allow user defined scale estimator
* :ghissue:`98`: check connection or connect summary to variable names in wrappers
* :ghissue:`418`: BUG: MNLogit loglikeobs, jac
* :ghissue:`1017`: nosetests warnings
* :ghissue:`924`: DOCS link in notebooks to notebook for download
* :ghissue:`1011`: power ttest endless loop possible
* :ghissue:`907`: BLD data_files for stats.libqsturng
* :ghissue:`328`: consider moving example scripts into IPython notebooks
* :ghissue:`1002`: Docs won't build with Sphinx 1.1.3
* :ghissue:`69`: Make methods like compare_ftest work with wrappers
* :ghissue:`503`: summary_old in RegressionResults
* :ghissue:`991`: TST precision of normal_power
* :ghissue:`945`: Installing statsmodels from github?
* :ghissue:`964`: Prefer to_offset not get_offset in tsa stuff
* :ghissue:`983`: bug: pandas 0.8.1 incompatibility
* :ghissue:`899`: build_ext inplace doesn't cythonize
* :ghissue:`923`: location of initialization code
* :ghissue:`980`: auto lag selection in  S_hac_simple
* :ghissue:`968`: genericMLE Ubuntu test failure
* :ghissue:`633`: python 3.3 compatibility
* :ghissue:`728`: test failure for solve_power with fsolve
* :ghissue:`971`: numdiff test cases
* :ghissue:`976`: VAR Model does not work in 1D
* :ghissue:`972`: numdiff: epsilon has no minimum value
* :ghissue:`967`: lowes test failure Ubuntu
* :ghissue:`948`: nonparametric tests: mcnemar, cochranq unit test
* :ghissue:`963`: BUG in runstest_2sample
* :ghissue:`946`: Issue with lowess() smoother in statsmodels
* :ghissue:`868`: k_vars > nobs
* :ghissue:`917`: emplike emplikeAFT stray dimensions
* :ghissue:`264`: version comparisons need to be made more robust (may be just use LooseVersion)
* :ghissue:`674`: failure in test_foreign, pandas testing
* :ghissue:`828`: GLMResults inconsistent distribution in pvalues
* :ghissue:`908`: RLM missing test for tvalues, pvalues
* :ghissue:`463`: formulas missing in docs
* :ghissue:`256`: discrete Nbin has zero test coverage
* :ghissue:`831`: test errors running bdist
* :ghissue:`733`: Docs: interrater cohens_kappa is missing
* :ghissue:`897`: lowess failure - sometimes
* :ghissue:`902`: test failure tsa.filters  precision too high
* :ghissue:`901`: test failure stata_writer_pandas, newer versions of pandas
* :ghissue:`900`: ARIMA.__new__   errors on python 3.3
* :ghissue:`832`: notebook errors
* :ghissue:`867`: Baxter King has unneeded limit on value for low?
* :ghissue:`781`: discreteResults margeff method not tests, obsolete
* :ghissue:`870`: discrete unit tests duplicates
* :ghissue:`630`: problems in regression plots
* :ghissue:`885`: Caching behavior for KDEUnivariate icdf
* :ghissue:`869`: sm.tsa.ARMA(..., order=(p,q)) gives "__init__() got an unexpected keyword argument 'order'" error
* :ghissue:`783`: statsmodels\distributions\mixture_rvs.py    no unit tests
* :ghissue:`824`: Multicomparison w/Pandas Series
* :ghissue:`789`: presentation of multiple comparison results
* :ghissue:`764`: BUG: multipletests incorrect reject for Holm-Sidak
* :ghissue:`766`: multipletests - status and tests of 2step FDR procedures
* :ghissue:`763`: Bug: multipletests raises exception with empty array
* :ghissue:`840`: sm.load should be in the main API namespace
* :ghissue:`830`: invalid version number
* :ghissue:`821`: Fail gracefully when extensions are not built
* :ghissue:`204`: Cython extensions built twice?
* :ghissue:`689`: tutorial notebooks
* :ghissue:`740`: why does t_test return one-sided p-value
* :ghissue:`804`: What goes in statsmodels.formula.api?
* :ghissue:`675`: Improve error message for ARMA SVD convergence failure.
* :ghissue:`15`: arma singular matrix
* :ghissue:`559`: Add Rdatasets to optional dependencies list
* :ghissue:`796`: Prediction Standard Errors
* :ghissue:`793`: filters are not pandas aware
* :ghissue:`785`: Negative R-squared
* :ghissue:`777`: OLS residuals returned as Pandas series when endog and exog are Pandas series
* :ghissue:`770`: Add ANOVA to docs
* :ghissue:`775`: Bug in dates_from_range
* :ghissue:`773`: AR model pvalues error with Pandas
* :ghissue:`768`: multipletests: numerical problems at threshold
* :ghissue:`355`: add draw if interactive to plotting functions
* :ghissue:`625`: Exog is not correctly handled in ARIMA predict
* :ghissue:`626`: ARIMA summary does not print exogenous variable coefficients
* :ghissue:`657`: order (0,1) breaks ARMA forecast
* :ghissue:`736`: ARIMA predict problem for ARMA model
* :ghissue:`324`: ic in ARResults, aic, bic, hqic, fpe inconsistent definition?
* :ghissue:`642`: sign_test   check
* :ghissue:`236`: AR start_params broken
* :ghissue:`235`: tests hang on Windows
* :ghissue:`156`: matplotlib deprecated legend ? var plots
* :ghissue:`331`: Remove stale tests
* :ghissue:`592`: test failures in datetools
* :ghissue:`537`: Var Models
* :ghissue:`755`: Unable to access AR fit parameters when model is estimated with pandas.DataFrame
* :ghissue:`670`: discrete: numerically useless clipping
* :ghissue:`515`: MNLogit residuals raise a TypeError
* :ghissue:`225`: discrete models only define deviance residuals
* :ghissue:`594`: remove skiptest in TestProbitCG
* :ghissue:`681`: Dimension Error in discrete_model.py When Running test_dummy_*
* :ghissue:`744`: DOC: new_f_test
* :ghissue:`549`: Ship released patsy source in statsmodels
* :ghissue:`588`: patsy is a hard dependency?
* :ghissue:`716`: Tests missing for functions if pandas is used
* :ghissue:`715`: statmodels regression plots not working with pandas datatypes
* :ghissue:`450`: BUG: full_output in optimizers Likelihood model
* :ghissue:`709`: DOCstrings linear models don't have missing params
* :ghissue:`370`: BUG weightstats has wrong cov
* :ghissue:`694`: DiscreteMargins duplicate method
* :ghissue:`702`: bug, pylint stats.anova
* :ghissue:`423`: Handling of constant across models
* :ghissue:`456`: BUG: ARMA date handling incompatibility with recent pandas
* :ghissue:`514`: NaNs in Multinomial
* :ghissue:`405`: Check for existing old version of scikits.statsmodels?
* :ghissue:`586`: Segmentation fault with OLS
* :ghissue:`721`: Unable to run AR on named time series objects
* :ghissue:`125`: caching pinv_wexog broke iterative fit - GLSAR
* :ghissue:`712`: TSA bug with frequency inference
* :ghissue:`319`: Timeseries Frequencies
* :ghissue:`707`: .summary with alpha ignores parsed value
* :ghissue:`673`: nonparametric: bug in _kernel_base
* :ghissue:`710`: test_power failures
* :ghissue:`706`: .conf_int() fails on linear regression without intercept
* :ghissue:`679`: Test Baxter King band-pass filter fails with scipy 0.12 beta1
* :ghissue:`552`: influence outliers breaks when regressing on constant
* :ghissue:`639`: test folders not on python path
* :ghissue:`565`: omni_normtest doesn't propagate the axis argument
* :ghissue:`563`: error in doc generation for AR.fit
* :ghissue:`109`: TestProbitCG failure on Ubuntu
* :ghissue:`661`: from scipy import comb fails on the latest scipy 0.11.0
* :ghissue:`413`: DOC: example_discrete.py missing from 0.5 documentation
* :ghissue:`644`: FIX: factor plot + examples broken
* :ghissue:`645`: STY: pep8 violations in many examples
* :ghissue:`173`: doc sphinx warnings
* :ghissue:`601`: bspline.py dependency on old scipy.stats.models
* :ghissue:`103`: ecdf and step function conventions
* :ghissue:`18`: Newey-West sandwich covariance is missing
* :ghissue:`279`: cov_nw_panel not tests, example broken
* :ghissue:`150`: precision in test_discrete.TestPoissonNewton.test_jac ?
* :ghissue:`480`: rescale loglike for optimization
* :ghissue:`627`: Travis-CI support for scipy
* :ghissue:`622`: mark tests as slow in emplike
* :ghissue:`589`: OLS F-statistic error
* :ghissue:`572`: statsmodels/tools/data.py Stuck looking for la.py
* :ghissue:`580`: test errors in graphics
* :ghissue:`577`: PatsyData detection buglet
* :ghissue:`470`: remove deprecated features
* :ghissue:`573`: lazy imports are (possibly) very slow
* :ghissue:`438`: New results instances are not in online documentation
* :ghissue:`542`: Regression plots fail when Series objects passed to sm.OLS
* :ghissue:`239`: release 0.4.x
* :ghissue:`530`: l1 docs issues
* :ghissue:`539`: test for statwriter (failure)
* :ghissue:`490`: Travis CI on PRs
* :ghissue:`252`: doc: distributions.rst refers to sandbox only
* :ghissue:`85`: release 0.4
* :ghissue:`65`: MLE fit of AR model has no tests
* :ghissue:`522`: ``test`` doesn't propagate arguments to nose
* :ghissue:`517`: missing array conversion or shape in linear model
* :ghissue:`523`: test failure with ubuntu decimals too large
* :ghissue:`520`: web site documentation, source not updated
* :ghissue:`488`: Avoid cholesky decomposition of diagonal matrices in linear regression models
* :ghissue:`394`: Definition in macrodata NOTE
* :ghissue:`45`: numdifftools dependency
* :ghissue:`501`: WLS/GLS post estimation results
* :ghissue:`500`: WLS fails if weights is a pandas.Series
* :ghissue:`27`: add hasconstant indicator for R-squared and df calculations
* :ghissue:`497`: DOC: add patsy?
* :ghissue:`495`: ENH: add footer SimpleTable
* :ghissue:`402`: model._data -> model.data?
* :ghissue:`477`: VAR NaN Bug
* :ghissue:`421`: Enhancment: Handle Missing Data
* :ghissue:`489`: Expose model._data as model.data
* :ghissue:`315`: tsa models assume pandas object indices are dates
* :ghissue:`440`: arima predict is broken for steps > q and q != 1
* :ghissue:`458`: TST BUG?   comparing pandas and array in tests, formula
* :ghissue:`464`: from_formula signature
* :ghissue:`245`: examples in docs: make nicer
* :ghissue:`466`: broken example, pandas
* :ghissue:`57`: Unhelpful error from bad exog matrix in model.py
* :ghissue:`271`: ARMA.geterrors requires model to be fit
* :ghissue:`350`: Writing to array returned np.diag
* :ghissue:`354`: example_rst does not copy unchanged files over
* :ghissue:`467`: Install issues with Pandas
* :ghissue:`444`: ARMA example on stable release website not working
* :ghissue:`377`: marginal effects count and discrete adjustments
* :ghissue:`426`: "svd" method not supported for OLS.fit()
* :ghissue:`409`: Move numdiff out of the sandbox
* :ghissue:`416`: Switch to complex-step Hessian for AR(I)MA
* :ghissue:`415`: bug in kalman_loglike_complex
* :ghissue:`397`: plot_corr axis text labeling not working (with fix)
* :ghissue:`399`: discrete errors due to incorrect in-place operation
* :ghissue:`389`: VAR test_normality is broken with KeyError
* :ghissue:`388`: Add tsaplots to graphics.api as graphics.tsa
* :ghissue:`387`: predict date wasn't getting set with start = None
* :ghissue:`386`: p-values not returned from acf
* :ghissue:`385`: Allow AR.select_order to work without model being fit
* :ghissue:`383`: Move mixture_rvs out of sandbox.
* :ghissue:`248`: ARMA breaks with a 1d exog
* :ghissue:`273`: When to give order for AR/AR(I)MA
* :ghissue:`363`: examples folder -> tutorials folder
* :ghissue:`346`: docs in sitepackages
* :ghissue:`353`: PACF docs raise a sphinx warning
* :ghissue:`348`: python 3.2.3 test failure zip_longest
