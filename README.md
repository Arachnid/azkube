# azkube


## Overview
	Tool used to deploy and bootstrap a Kubernetes cluster in Azure.


## Running
	```
	docker run -it \
		colemickens/azkube:latest /opt/azkube/azkube \
			--tenant-id="{your tenant id}" \
			--subscription-id="{subscription id}"
	```

## Usage

[ insert generated output from cobra's command facilities ]


## Motivations
1. Existing shell script was fragile.
2. azure-xplat-cli changes out from underneath of us, and is slow, and doesn't handle errors well.
3. Need a tool and process to create service principals and configure them appropriately.
4. Need a tool to consume scripts, interweave ARM template variables/parameters, and
   output a "deployable" template.

## Future

As Azure lands support for managed service identity and metadata facilities, some of the need for this tool will be alleviated.

## Known Issues
1. Needs tests
2. `scale` is not implemented
3. `destroy` is not implemented


## Potential Future Improvements
1. Add an Ubuntu 16.04 Flavor (lets us avoid `upstart`)
2. Validate `location`, `master-size`, `node-size` against the live services


## Bugs
1. `kube-controller-manager` seems to flat out ignore my `kubeconfig` file.
   The other components all read the `kubeconfig` file and connect correctly.
   Skimming the source, I see a lack of warning about missing `kubeconfig` file,
   and no permission or opening errors, so I'm wondering if there's a bug in the
   `hyperkube:v1.1.8` image.

2. As a result of #1, the insecure port is still open on the apiserver. (Though
   it is firewalled to the outside world.

## Important Notes
1. The user who executes the application must have permission to provision
   additional applications. This is difficult to achieve unless you're using
   the device auth method (`--auth-method=device` which is the default). If you
   wish to automate the use of this tool to remove all interactivity, you must create
   a new Application in your Azure Active Directory Tenant. Then you must use the ADAL
   Powershell Toolkit to grant the service principal associated with the application
   the AD Role "Company User".

2. The resulting "templates" are fully parameterized and generic. They can be uploaded
   and used by others. The values that are entered for the parameters are interpolated
   into the cloud-config scripts which are then interpolated into the ARM Templates.
   These are copied to the deployment directory so that they might be reused (if desired).
