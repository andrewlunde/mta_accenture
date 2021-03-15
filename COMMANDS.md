cf jq acc-pyt-sch | jq .VCAP_SERVICES > vcap_svcs_a.json
cp vcap_svcs_a.json vcap_svcs_b.json
sed -i -e "s/ffa845c4-c70e-4774-ba37-c0a6fdf16850/2e77e6fd-d51b-473e-8a3f-9f425e18448f/g" vcap_svcs_b.json
cf set-env acc-pyt-sch VCAP_SERVICES
cf set-env acc-pyt-sch VCAP_SERVICES '{"yo":"ma"}'
cf enable-ssh acc-pyt-sch
cf restart acc-pyt-sch
cf ssh acc-pyt-sch
