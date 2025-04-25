FROM ckan/ckan-base:2.11

apt update && apt install build-essential python3-dev libssl-dev swig
# Install any extensions needed by your CKAN instance
### ckanext-azure-auth ###
RUN  pip3 install -e git+https://github.com/datopian/ckanext-azure-auth.git@ckan-2.10.3 && \
     pip3 install -r https://raw.githubusercontent.com/datopian/ckanext-azure-auth/refs/heads/ckan-2.10.3/requirements.txt

# See Dockerfile.dev for more details and examples

# Copy custom initialization scripts
COPY --chown=ckan-sys:ckan-sys docker-entrypoint.d/* /docker-entrypoint.d/
COPY --chown=ckan-sys:ckan-sys setup/start_ckan.sh.gd.override ${APP_DIR}/start_ckan.sh
# Apply any patches needed to CKAN core or any of the built extensions (not the
# runtime mounted ones)
COPY --chown=ckan-sys:ckan-sys patches ${APP_DIR}/patches

USER ckan

RUN for d in $APP_DIR/patches/*; do \
        if [ -d $d ]; then \
            for f in `ls $d/*.patch | sort -g`; do \
                cd $SRC_DIR/`basename "$d"` && echo "$0: Applying patch $f to $SRC_DIR/`basename $d`"; patch -p1 < "$f" ; \
            done ; \
        fi ; \
    done
