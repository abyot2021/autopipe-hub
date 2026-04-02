FROM condaforge/miniforge3:latest

# Install bioinformatics tools
RUN conda install -y -c bioconda -c conda-forge \
    snakemake-minimal \
    bash \
    bcftools=1.21 \
    htslib=1.21 \
    vcflib=1.0.3 \
    cas-offinder=2.4.1 \
    samtools=1.21 \
    && conda clean -afy

# Replace system bash with conda bash (prevents GLIBC mismatch)
RUN ln -sf /opt/conda/bin/bash /usr/bin/bash && \
    ln -sf /opt/conda/bin/bash /bin/sh

# Setup pipeline
WORKDIR /pipeline
COPY Snakefile .
COPY config.yaml .

CMD ["snakemake", "--help"]