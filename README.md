# Custom Monitor: VoIP over SIP

## Introduction

Voice over IP (VoIP) is the technology responsible for delivering voice and multimedia sessions over the internet. In recent years there has been amazing advances in this field enabling better quality audio sessions. Even with these advances, packet loss remains as a major concern for performance degradation. Our new custom monitor captures Session Initiation Protocol (SIP) metrics, packet loss & jitter, and Round Trip Time to calculate the quality of the audio session over SIP. These metrics are used to calculate the Mean Opinion Score (MOS) for a VoIP SIP call. The custom monitor relies on SIP Simple Client SDK and Client, find more details about the SDK [here](https://sipsimpleclient.org/).

This monitor captures the following metrics to gauge the performance of Session Initiation Protocol (SIP) and Real Time Protocol (RTP).

**Session Request Delay (SDR)**: SDR is the time difference between the client initiating a SIP INVITE request and the client receiving a 180 Ringing response. Reported in milliseconds.

**Session Negotiation Time (SNT)**: SNT is the time difference between the client initiating a SIP INVITE request and the client receiving 200 OK. Reported in milliseconds.

**Session Duration Time (SDT)**: SDT is the total time spent on the call with RTP data transmitted. Reported in milliseconds.

**Session Disconnect Delay (SDD)**:  SDD is the time difference the client initiating a SIP BYE request and the client receiving 200 OK. This is the last metric collected for SIP.

**Round Trip Time (RTT)**: RTT is the time difference the client initiating a request and the client receiving a response. Reported in milliseconds.

**Packet Loss**: Percentage of total packet loss during the audio session.

**Jitter TX/RX**: Jitter is the delay between the expected packet delivery time and the actual packet delivery time. Reported in milliseconds.

**Mean Opinion Score (MOS) TX/RX**: MOS is the calculated audio session quality score. The score has a range from 1 to 5, with 1 being the lowest and 5 the highest score.

## Prerequisites

`Python 2.7` – installed and used by Catchpoint enterprise nodes.  
`yum` – latest available version.  
`pip` – latest available version.  
`SIP Server/Client` to auto accepts calls from a SIP Client.  

## Installation & Configuration

Download the [`Install`](install) and [`SIP Collect Metrics`](SIP%20Collect%20Metrics) files.
Save to `/opt/3genlabs/hawk/syntheticnode/service/shellmonitor/sandbox` on your Enterprise Node.

### Install all the dependencies required for the set up.

1. Update `yum` and `pip` to the latest version.  
`$ yum update -y`  
`$ pip install --upgrade pip`

1. Change the permissions for the `Install` file.  
`$ chmod a+x Install`

1. Run the `Install` file to install all the dependent packages.  
`$ sudo ./Install`

1. To install the packages, the script relies on `yum`, `pip` and `git`. Below is the list of all packages that will be installed on the machine:
    ```
    python  [http://python.org](http://python.org/)  2.7
    python-application  [http://pypi.python.org/simple/python-application](http://pypi.python.org/simple/python-application)  >=2.8.0
    python-backports  [http://download.ag-projects.com/SipClient](http://download.ag-projects.com/SipClient)  >=1.0.0
    python-cjson  [http://pypi.python.org/pypi/python-cjson/](http://pypi.python.org/pypi/python-cjson/)  >=1.0.5
    python-dateutil  [http://niemeyer.net/python-dateutil](http://niemeyer.net/python-dateutil)  >=1.4
    python-eventlib  [http://download.ag-projects.com/SipClient](http://download.ag-projects.com/SipClient)  >=0.1.1
    python-greenlet  [http://download.ag-projects.com/SipClient](http://download.ag-projects.com/SipClient)  =0.4.0
    python-gnutls  [http://pypi.python.org/simple/python-gnutls](http://pypi.python.org/simple/python-gnutls)  >=1.1.9
    python-lxml  [http://codespeak.net/lxml](http://codespeak.net/lxml)  >=2.1.2
    python-msrplib  [http://download.ag-projects.com/MSRP](http://download.ag-projects.com/MSRP)  >=0.20.0
    python-xcaplib  [http://download.ag-projects.com/XCAP](http://download.ag-projects.com/XCAP)  >=1.2.2
    python-otr  [http://download.ag-projects.com/](http://download.ag-projects.com/)  >=1.2.0
    cython  [http://www.cython.org](http://www.cython.org/)  >=0.13.0
    dnspython  [http://www.dnspython.org](http://www.dnspython.org/)  >=1.9.0
    twisted  [http://twistedmatrix.com/trac](http://twistedmatrix.com/trac)  >=8.1.0
    zope-interface  [http://www.zope.org](http://www.zope.org/)  >=3.3.1
    ```

1. Once the installation is complete, run the `SIP Client` command to verify the packages are installed successfully:  
`$ sip-settings`

1. Configure a `SIP account` for initiating calls.  
`$ sip-settings -a add <user@domain> <password>`

1. Set the default account.  
`$ sip-settings -a default <user@domain>`

1. Test the setup with the below command.  
`$ sip-audio-session`

### Configure the Enterprise Node

1. Change the file permission and owner to allow script execution. The below commands gives permission to `serveruser` to execute the script `SIP Collect Metrics`.  
`$ chmod 500 SIP Collect Metrics`  
`$ chown serveruser SIP Collect Metrics`

1. Change the file permission and owner to allow `serveruser` to access the SDK.
    1. Open the `/etc/sudoers` file  
    `$ sudo vi /etc/sudoers` 

    1. This file allows us to define the policies applied by the sudo command.  
    Uncomment `wheel` group entries to grant `sudo` access to the specified users.
        ```
        ## Allows people in group wheel to run all commands
        %wheel    ALL=(ALL)    ALL

        ## Same thing without a password  
        %wheel    ALL=(ALL)    NOPASSWD: ALL
        ```

    1. Save the file and exit.

    1. Add `serveruser` to the Wheel Group using the command:  
    `$ usermod -aG wheel serveruser`

1. Create and change permissions of log file for `serveruser` to collect and store logs:  
`$ touch call.log`  
`$ chmod 777 call.log`  
`$ chown serveruser call.log`  

### Configure Catchpoint Portal

1. Login to the [Catchpoint portal](https://portal.catchpoint.com/ui/Entry/Login.aspx)

1. Navigate to the Test Module.

1. Create a new test by selecting `Custom Monitor` in the `Create Test` dropdown.  

1. Configure your test. [Custom Monitor Overview](https://support.catchpoint.com/hc/en-us/articles/360004586331)
    1. Enter a test name.
    
    1. Select Custom Monitor type.
    
    1. Configure additional options.
    
    1. Enter the Script filename - `SIP Collect Metrics`.  
    _Note: If you have changed the file name, ensure to change this entry as well._
    
1. Create two custom fields and enter their values as below:
    ```
    | Variable Name |  Value              |  
    |---------------|---------------------|  
    | SIPADDRESSTO  | <SIP Address to>    |  
    | DURATION      | <Between 10 to 100> |  
    ```
1. Under Targeting and Scheduling, select the Enterprise Node configured above.

1. Save your test.

Once the test it set up in Catchpoint portal our system will capture metrics using Insights. After test data is collected, the audio session performance data can be retrieved and analyzed in the Catchpoint portal. Catchpoint also provides [Alerts](https://support.catchpoint.com/hc/en-us/articles/203646755) that can be defined in the test configuration. For example, if you have set up Alerts for performance degradation when the test is run, our Alerting service will trigger an alert delivered to the configured people.
