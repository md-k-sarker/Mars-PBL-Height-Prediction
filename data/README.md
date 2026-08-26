# External data

Data files are not stored in Git. Populate this directory from the original data distributions using the layout expected by the experiment code.

## Mars Climate Database (MCD) 6.1 setup

The experiments use the full Mars Climate Database (MCD) version 6.1 installed locally. After download, they query the locally installed MCD database through its Python interface using `MCD.call_mcd_entry()`.

### Download instructions

1. Go to the official Mars Climate Database access page:
   https://www-mars.lmd.jussieu.fr/mars/access.html

2. Under **MCD 6.1 Full version**, select the link for the full database.

3. Complete the MCD registration form with your name, institution, email address, and intended scientific use.

4. Accept the MCD distribution conditions and submit the form.

5. Download and extract the full MCD 6.1 distribution.

6. To run the experiments, the MCD Python interface comes with MCD 6.1 and needs to be built/configured for the local operating system and Python installation.

### Directory layout

```text
data/
└── Mars/
    ├── MCD/
    │   └── MCD_6.1/
    │       ├── data/
    │       └── ...
    └── OpenMARS/
        └── pblh_labels/
            └── openmars_pblh_merged.nc
```

MCD experiments also require a locally compiled Python extension compatible with the active Python interpreter and platform. Follow the documentation supplied with MCD 6.1.

OpenMARS experiments require the OpenMARS dataset and a label-generation script to compute planetary boundary layer height labels. The script is available upon request — see the repository root README for contact details. Place the resulting `openmars_pblh_merged.nc` file in `data/Mars/OpenMARS/pblh_labels/`.
