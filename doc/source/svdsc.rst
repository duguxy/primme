
.. role:: ccode(code) 
   :language: c

.. highlight:: c

C Library Interface
-------------------

The PRIMME SVDS interface is composed of the following functions.
To solve real and complex singular value problems call respectively:

.. only:: not text

   .. parsed-literal::

      int :c:func:`dprimme_svds <dprimme_svds>` (double \*svals, double \*svecs, double \*resNorms,
                              primme_svds_params \*primme_svds)
      int :c:func:`zprimme_svds <zprimme_svds>` (double \*svals, Complex_Z \*svecs, double \*resNorms,
                              primme_svds_params \*primme_svds)

.. only:: text

   ::

      int dprimme_svds(double *svals, double *svecs, double *resNorms, 
                  primme_svds_params *primme);

      int zprimme_svds(double *svals, Complex_Z *svecs, double *resNorms, 
                  primme_svds_params *primme);

Other useful functions:

.. only:: not text

   .. parsed-literal::

      void :c:func:`primme_svds_initialize <primme_svds_initialize>` (primme_svds_params \*primme_svds)
      int :c:func:`primme_svds_set_method <primme_svds_set_method>` (primme_svds_preset_method method,
         primme_preset_method methodStage1, primme_preset_method methodStage2, primme_svds_params \*primme_svds)
      void :c:func:`primme_svds_display_params <primme_svds_display_params>` (primme_svds_params primme_svds)
      void :c:func:`primme_svds_Free <primme_svds_Free>` (primme_svds_params \*primme_svds)

.. only:: text

   ::

      void primme_svds_initialize(primme_svds_params *primme_svds);
      int primme_svds_set_method(primme_svds_preset_method method,
            primme_preset_method methodStage1, primme_preset_method methodStage2,
            primme_svds_params *primme_svds);
      void primme_svds_display_params(primme_svds_params primme_svds);
      void primme_svds_Free(primme_svds_params *primme_svds);

PRIMME SVDS stores its data on the structure :c:type:`primme_svds_params`.
See :ref:`svds-guide-params` for an introduction about its fields.


Running
^^^^^^^

To use PRIMME SVDS, follow this basic steps.

#. Include::

      #include "primme.h"   /* header file is required to run primme */

#. Initialize a PRIMME SVDS parameters structure for default settings:

   .. only:: not text
   
       .. parsed-literal::

          :c:type:`primme_svds_params` primme_svds;
          :c:func:`primme_svds_initialize <primme_svds_initialize>` (&primme_svds);

   .. only:: text
   
      ::
   
         primme_svds_params primme_svds;
         
         primme_svds_initialize(&primme_svds);
   
#. Set problem parameters (see also :ref:`svds-guide-params`), and,
   optionally, set one of the :c:type:`preset methods <primme_svds_preset_method>`:

   .. only:: not text

      .. parsed-literal::

         primme_svds.\ |SmatrixMatvec| = matrixMatvec; /\* MV product \*/
         primme_svds.\ |Sm| = 1000;                    /\* set the matrix dimensions \*/
         primme_svds.\ |Sn| = 100;
         primme_svds.\ |SnumSvals| = 10;         /\* Number of wanted singular values \*/
         :c:func:`primmesvds_set_method <primme_svds_set_method>` (method, DEFAULT_METHOD, DEFAULT_METHOD, &primme_svds);
         ...

   .. only:: text

      ::

         primme_svds.matrixMatvec = matrixMatvec; /* MV product */
         primme_svds.m = 1000;                    /* set problem dimension */
         primme_svds.n = 100;
         primme_svds.numSvals = 10;    /* Number of wanted singular values */
         primme_svds_set_method(method, DEFAULT_METHOD, DEFAULT_METHOD, &primme_svds);
         ...

#. Then to solve a real singular value problem call:

   .. only:: not text
  
      .. parsed-literal::
 
         ret = :c:func:`dprimme_svds <dprimme_svds>` (svals, svecs, resNorms, &primme_svds);
   
   .. only:: text
   
      ::
   
         ret = dprimme_svds(svals, svecs, resNorms, &primme_svds);

   To solve complex singular value problems call:

   .. only:: not text
   
      .. parsed-literal::

         ret = :c:func:`zprimme_svds <zprimme_svds>` (svals, svecs, resNorms, &primme_svds);
   
   .. only:: text
   
      ::
   
         ret = zprimme_svds(svals, svecs, resNorms, &primme_svds);

   The call arguments are:

   * `svals`, array to return the found singular values;
   * `svecs`, array to return the found left and right singular vectors;
   * `resNorms`, array to return the residual norms of the found triplets; and
   * `ret`, returned error code.

#. Before exiting, free the work arrays in PRIMME SVDS:

   .. only:: not text
  
      .. parsed-literal::
 
         :c:func:`primme_svds_Free <primme_svds_Free>` (&primme_svds);
   
   .. only:: text
   
      ::
   
         primme_svds_Free(&primme_svds);

.. _svds-guide-params:

Parameters Guide
^^^^^^^^^^^^^^^^

PRIMME SVDS stores the data on the structure :c:type:`primme_svds_params`, which has the next fields:
   
.. only:: not text

      | *Basic*
      | ``int`` |Sm|,  number of rows of the matrix.
      | ``int`` |Sn|,  number of columns of the matrix.
      | ``void (*`` |SmatrixMatvec| ``)(...)``, matrix-vector product.
      | ``int`` |SnumSvals|, how many singular triplets to find.
      | ``primme_svds_target`` |Starget|, which singular values to find.
      | ``double`` |Seps|, tolerance of the residual norm of converged triplets.
      |
      | *For parallel programs*
      | ``int`` |SnumProcs|
      | ``int`` |SprocID|
      | ``int`` |SmLocal|
      | ``int`` |SnLocal|
      | ``void (*`` |SglobalSumDouble| ``)(...)``
      |
      | *Accelerate the convergence*
      | ``void (*`` |SapplyPreconditioner| ``)(...)``, preconditioner-vector product.
      | ``int`` |SinitSize|, initial vectors as approximate solutions.
      | ``int`` |SmaxBasisSize|
      | ``int`` |SminRestartSize|
      | ``int`` |SmaxBlockSize|
      |
      | *User data*
      | ``void *`` |ScommInfo|
      | ``void *`` |Smatrix|
      | ``void *`` |Spreconditioner|
      |
      | *Advanced options*
      | ``int`` |SnumTargetShifts|, for targeting interior singular values.
      | ``double *`` |StargetShifts|
      | ``int`` |SnumOrthoConst|, orthogonal constrains to the singular vectors.
      | ``int`` |Slocking|
      | ``int`` |SmaxMatvecs|
      | ``int`` |SintWorkSize|
      | ``long int`` |SrealWorkSize|
      | ``int`` |Siseed| ``[4]``
      | ``int *`` |SintWork|
      | ``void *`` |SrealWork|
      | ``double`` |SaNorm|
      | ``int`` |SprintLevel|
      | ``FILE *`` |SoutputFile|
      | ``primme_svds_operator`` |Smethod|
      | ``primme_svds_operator`` |SmethodStage2|
      | |primme_params| |Sprimme|
      | |primme_params| |SprimmeStage2|

.. only:: text

   ::

      /* Basic */
      int m;                           // number of rows of the matrix
      int n;                        // number of columns of the matrix
      void (*matrixMatvec)(...);              // matrix-vector product
      int numSvals;              // how many singular triplets to find
      primme_svds_target target;      // which singular values to find
      double eps;               // tolerance of the converged triplets
      
      /* For parallel programs */
      int numProcs;
      int procID;
      int mLocal;
      int nLocal;
      void (*globalSumDouble)(...);
      
      /* Accelerate the convergence */
      void (*applyPreconditioner)(...); // preconditioner-vector product
      int initSize;        // initial vectors as approximate solutions
      int maxBasisSize;
      int minRestartSize;
      int maxBlockSize;
      
      /* User data */
      void *commInfo;
      void *matrix;
      void *preconditioner;
      
      /* Advanced options */
      int numTargetShifts;        // for targeting interior values
      double *targetShifts;
      int numOrthoConst;   // orthogonal constrains to the vectors
      int locking;
      int maxMatvecs;
      int intWorkSize;
      long int realWorkSize;
      int iseed[4];
      int *intWork;
      void *realWork;
      double aNorm;
      int printLevel;
      FILE * outputFile;
      primme_svds_operator method;
      primme_svds_operator methodStage2;
      primme_params primme;
      primme_params primmeStage2;


PRIMME SVDS requires the user to set at least the matrix dimensions (|Sm| x |Sn|) and
the matrix-vector product (|SmatrixMatvec|), as they define the problem to be solved.
For parallel programs, |SmLocal|, |SnLocal|, |SprocID| and |SglobalSumDouble| are also required.

In addition, most users would want to specify how many singular triplets to find,
and provide a preconditioner (if available).

It is useful to have set all these before calling :c:func:`primme_svds_set_method`.
Also, if users have a preference on |SmaxBasisSize|, |SmaxBlockSize|, etc, they
should also provide them into :c:type:`primme_svds_params` prior to the
:c:func:`primme_svds_set_method` call. This helps :c:func:`primme_svds_set_method` make
the right choice on other parameters. It is sometimes useful to check the actual
parameters that PRIMME SVDS is going to use (before calling it) or used (on return)
by printing them with :c:func:`primme_svds_display_params`.

Interface Description
^^^^^^^^^^^^^^^^^^^^^

The next enumerations and functions are declared in ``primme.h``.

dprimme_svds
""""""""""""

.. c:function:: int dprimme_svds(double *svals, double *svecs, double *resNorms, primme_svds_params *primme_svds)

   Solve a real singular value problem.

   :param svals: array at least of size |SnumSvals| to store the
      computed singular values; all processes in a parallel run return this local array with the same values.

   :param resNorms: array at least of size |SnumSvals| to store the
      residual norms of the computed triplets; all processes in parallel run return this local array with
      the same values.

   :param svecs: array at least of size (|SmLocal| + |SnLocal|) times |SnumSvals|
      to store columnwise the (local part of the) computed left singular vectors
      and the right singular vectors.

   :param primme_svds: parameters structure.

   :return: error indicator; see :ref:`error-codes-svds`.

zprimme_svds
""""""""""""

.. c:function:: int zprimme_svds(double *svals, Complex_Z *svecs, double *resNorms, primme_svds_params *primme_svds)

   Solve a complex singular value problem; see function :c:func:`dprimme_svds`.

   .. note::

      PRIMME SVDS uses a structure called ``Complex_Z`` to define complex numbers.
      ``Complex_Z`` is defined in :file:`PRIMMESRC/COMMONSRC/Complexz.h`.
      In future versions of PRIMME, ``Complex_Z`` will be replaced by ``complex double`` from
      the C99 standard.
      Because the two types are binary compatible, we strongly recommend that calling
      programs use the C99 type to maintain future compatibility.
      See examples in :file:`TEST` such as :file:`exsvds_zseq.c` and :file:`exsvds_zseqf77.c`.

primme_svds_initialize
""""""""""""""""""""""

.. c:function:: void primme_svds_initialize(primme_svds_params *primme_svds)

   Set PRIMME SVDS parameters structure to the default values.

   :param primme_svds: parameters structure.

primme_svds_set_method
""""""""""""""""""""""

.. c:function:: int primme_svds_set_method(primme_svds_preset_method method, primme_preset_method methodStage1, primme_preset_method methodStage2, primme_svds_params *primme_svds)

   Set PRIMME SVDS parameters to one of the preset configurations.

   :param method: preset method to compute the singular triplets; one of

      * |primme_svds_default|, currently set as |primme_svds_hybrid|.
      * |primme_svds_normalequations|, compute the eigenvectors of :math:`A^*A` or :math:`A A^*`.
      * |primme_svds_augmented|, compute the eigenvectors of the augmented matrix, :math:`\left(\begin{array}{} 0 & A^* \\ A & 0 \end{array}\right)`.
      * |primme_svds_hybrid|, start with |primme_svds_normalequations|; use the
        resulting approximate singular vectors as initial vectors for
        |primme_svds_augmented| if the required accuracy was not achieved.

   :param methodStage1: preset method to compute the eigenpairs at the first stage; see available values at :c:func:`primme_set_method`.

   :param methodStage2: preset method to compute the eigenpairs with
      the second stage of ``primme_svds_hybrid``; see available values at :c:func:`primme_set_method`.

   :param primme_svds: parameters structure.

   See also :ref:`methods_svds`.

primme_svds_display_params
""""""""""""""""""""""""""

.. c:function:: void primme_svds_display_params(primme_svds_params primme_svds)

   Display all printable settings of ``primme_svds`` into the file descriptor |SoutputFile|.

   :param primme_svds: parameters structure.

primme_svds_Free
""""""""""""""""

.. c:function:: void primme_svds_Free(primme_svds_params *primme_svds)

   Free memory allocated by PRIMME SVDS.

   :param primme_svds: parameters structure.

.. include:: epilog.inc