FROM gurongyun/weblogic:12.2.1.3-generic

ARG CUSTOM_DOMAIN_NAME="${CUSTOM_DOMAIN_NAME:-domain1}"
ARG CUSTOM_ADMIN_PORT="${CUSTOM_ADMIN_PORT:-7001}"
ARG CUSTOM_MANAGED_SERVER_PORT="${CUSTOM_MANAGED_SERVER_PORT:-8001}"
ARG CUSTOM_DEBUG_PORT="${CUSTOM_DEBUG_PORT:-8453}"
ARG CUSTOM_ADMIN_NAME="${CUSTOM_ADMIN_NAME:-admin-server}"
ARG CUSTOM_ADMIN_HOST="${CUSTOM_ADMIN_HOST:-admin-server}"
ARG CUSTOM_CLUSTER_NAME="${CUSTOM_CLUSTER_NAME:-cluster1}"
ARG CUSTOM_MANAGED_SERVER_NAME_BASE="${CUSTOM_MANAGED_SERVER_NAME_BASE:-managed-server}"

ENV ORCLE_NAME=/u01/oracle \
    PROPERTIES_FILE_DIR="/u01/oracle/properties" \
    DOMAIN_NAME="${CUSTOM_DOMAIN_NAME}" \
    DOMAIN_HOME="/u01/oracle/user_projects/domains/${CUSTOM_DOMAIN_NAME}" \
    ADMIN_PORT="${CUSTOM_ADMIN_PORT}" \
    ADMIN_NAME="${CUSTOM_ADMIN_NAME}" \
    ADMIN_HOST="${CUSTOM_ADMIN_HOST}" \
    CLUSTER_NAME="${CUSTOM_CLUSTER_NAME}" \
    CLUSTER_TYPE="${CUSTOM_CLUSTER_TYPE}" \
    MANAGED_SERVER_NAME_BASE=${CUSTOM_MANAGED_SERVER_NAME_BASE}\
    MANAGED_SERVER_PORT="${CUSTOM_MANAGED_SERVER_PORT}" \
    DEBUG_PORT="${CUSTOM_DEBUG_PORT}" \
    PATH=$PATH:/u01/oracle/oracle_common/common/bin:/u01/oracle/wlserver/common/bin:${DOMAIN_HOME}:${DOMAIN_HOME}/bin:/u01/oracle

COPY --chown=oracle:oracle container-scripts/* /u01/oracle/

USER root

RUN chmod +xw /u01/oracle/*.sh && \
    chmod +xw /u01/oracle/*.py && \
    mkdir -p ${PROPERTIES_FILE_DIR} && \
    chown -R oracle:oracle ${PROPERTIES_FILE_DIR} && \
    mkdir -p $DOMAIN_HOME && \
    chown -R oracle:oracle $DOMAIN_HOME/.. && \
    chmod -R a+xwr $DOMAIN_HOME/..

COPY --chown=oracle:oracle properties/domain*.properties ${PROPERTIES_FILE_DIR}/

USER oracle

RUN /u01/oracle/createWLSDomain.sh && \
    echo ". $DOMAIN_HOME/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc && \
    chmod -R a+x $DOMAIN_HOME/bin/*.sh  && \
    rm ${PROPERTIES_FILE_DIR}/*.properties

WORKDIR $DOMAIN_HOME

CMD ["startAdminServer.sh"]

