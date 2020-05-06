# env-config
Environmental configuration files and scripts


# Embedded Software Project Checklist

Phase 1 - Project Setup
- [ ] Setup revision control.
- [ ] Create the project.
- [ ] Create the project directory structure.
- [ ] Set the white and tab spacing for the project.

Phase 2 - Configuration
- [ ] Add Doxygen code templates (see `/Doxygen`).
- [ ] Configure Doxygen wizard.
- [ ] Import skeleton HA/API's.
- [ ] Create a Version log.
- [ ] Create a hardware configuration module.

Phase 3 - Code Analysis
- [ ] Setup static code analysis tool
- [ ] Setup software metrics analyzer
- [ ] Setup dynamic analysis tool (if one is available).

Phase 4 - Scheduler Setup
- [ ] Setup a RTOS or baremetal scheduler
    - [ ] Need to setup a system timer.
- [ ] Setup a single task to blink an LED (EE "Hello World")

Phase 5 - Debug Messages
- [ ] Setup RTT and trace analysis tool if available.
- [ ] Setup printf
    - [ ] UART driver
    - [ ] UART mapped to printf
- [ ] Configure assert.

Phase 6 - Begin Development
- [ ] Write quality, robust, secure, reusable and portable software.