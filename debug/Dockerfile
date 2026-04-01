FROM almalinux:9

# Install required packages
RUN dnf install -y telnet net-tools iputils && dnf clean all

# Create user
RUN useradd roboshop

# Switch to non-root user
USER roboshop

# Default command
CMD ["sleep", "1003"]