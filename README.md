https://ssm.hcloud.cl.bsch/oauth/token?grant_type=authorization_code&code=L7a9YL_bEAIiz-lKygp6UkDs9-7u0M0L&redirect_uri=https://chl-dss-lowcodeportal-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/

import React, { useEffect } from "react";

const DetectUrl: React.FC = () => {
  useEffect(() => {
    const url = new URL(window.location.href);
    const code = url.searchParams.get("code");
    if (code) {
      console.log("El parámetro code es:", code);
      // Aquí ya tienes el valor para guardarlo
    }
  }, []);
};

export default DetectUrl;
