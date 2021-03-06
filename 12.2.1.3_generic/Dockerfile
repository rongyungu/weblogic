FROM gurongyun/oralce-jre:8u221 as builder

ENV ORACLE_HOME=/u01/oracle \
    USER_MEM_ARGS="-java.security.egd=file:/dev/./urandom" \
    PATH=$PATH:/u01/oracle/oracle_common/common/bin:/u01/oracle/wlserver/common/bin

RUN mkdir -p /u01 && \
    chmod a+xr /u01 && \
    useradd -b /u01 -d /u01/oracle -m -s /bin/bash oracle

ENV FMW_PKG=fmw_12.2.1.3.0_wls_Disk1_1of1.zip \
    FMW_JAR=fmw_12.2.1.3.0_wls.jar

COPY $FMW_PKG install.file oraInst.loc /u01/

RUN chown oracle:oracle -R /u01

USER oracle

RUN cd /u01 && ${JAVA_HOME}/bin/jar xf /u01/$FMW_PKG && cd - && \
    ${JAVA_HOME}/bin/java -jar /u01/$FMW_JAR -silent -responseFile /u01/install.file -invPtrLoc /u01/oraInst.loc -jreLoc $JAVA_HOME -ignoreSysPrereqs -force -novalidation ORACLE_HOME=$ORACLE_HOME INSTALL_TYPE="WebLogic Server" && \
    rm /u01/$FMW_JAR /u01/$FMW_PKG /u01/install.file && \
    rm -rf /u01/oracle/cfgtoollogs

FROM gurongyun/oralce-jre:8u221

ENV ORACLE_HOME=/u01/oracle \
    USER_MEM_ARGS="-Djava.security.egd=file:/dev/./urandom" \
    SCRIPT_FILE=/u01/oracle/createAndStartEmptyDomain.sh \
    PATH=$PATH:${JAVA_HOME}/bin:/u01/oracle/oracle_common/common/bin:/u01/oracle/wlserver/common/bin

ENV DOMAIN_NAME="${DOMAIN_NAME:-base_domain}" \
    ADMIN_LISTEN_PORT="${ADMIN_LISTEN_PORT:-7001}" \
    ADMIN_NAME="${ADMIN_NAME:-AdminServer}" \
    ADMINISTRATION_PORT_ENABLED="${ADMINISTRATION_PORT_ENABLED:-true}" \
    ADMINISTRATION_PORT="${ADMINISTRATION_PORT:-9002}"

RUN mkdir -p /u01 && \
    chmod a+xr /u01 && \
    useradd -b /u01 -d /u01/oracle -m -s /bin/bash oracle && \
    chown oracle:oracle /u01

COPY --from=builder --chown=oracle:oracle /u01 /u01

COPY container-scripts/createAndStartEmptyDomain.sh container-scripts/create-wls-domain.py /u01/oracle/

RUN chmod +xr $SCRIPT_FILE && \
    chown oracle:oracle $SCRIPT_FILE /u01/oracle/create-wls-domain.py 

USER oracle

WORKDIR ${ORACLE_HOME}

CMD ["/u01/oracle/createAndStartEmptyDomain.sh"]
