# ManageX XML Signing SDK

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)](https://github.com/Aniketc068/managex_xml_sdk)

A comprehensive Python SDK for digital certificate management and XML digital signing with enterprise-grade security and multi-platform support.

## 🚀 Features

- ✅ **Multi-platform Support**: Windows, Linux, macOS
- ✅ **Multiple Certificate Sources**: Windows Store, PFX files, HSM tokens
- ✅ **Enterprise Security**: Cryptographic verification against trusted root CAs
- ✅ **XML Digital Signing**: Full XML-DSig standard (RFC 3275) compliance
- ✅ **Advanced Certificate Validation**: AKI/SKI matching, CRL/OCSP checking
- ✅ **Flexible Certificate Filtering**: By CN, Organization, Email, Serial Number, CA
- ✅ **HSM Token Support**: PKCS#11 compatible hardware security modules
- ✅ **User-Friendly**: Windows certificate selection dialog integration
- ✅ **Production Ready**: Comprehensive error handling and logging

## 📦 Installation

### From PyPI (Recommended)
```bash
pip install managex-xml-sdk
```

### From Source
```bash
git clone https://github.com/Aniketc068/managex_xml_sdk.git
cd managex_xml_sdk
pip install -e .
```

### Dependencies
```bash
pip install -r requirements.txt
```

## 🏃 Quick Start

### Basic XML Signing with Windows Certificate Store

```python
from managex_xml_sdk.core.xml_signer import XMLSigner

# Create signer with automatic certificate selection dialog
signer = XMLSigner.create(
    method="store",
    store="MY",
    trusted_roots_folder="root_certificates"
)

# Sign XML file - Windows dialog will appear for certificate selection
success = signer.sign_file("document.xml", "signed_document.xml")
print(f"Signing successful: {success}")
```

### Advanced Configuration

```python
from managex_xml_sdk import (
    XMLSigner,
    WindowsStoreConfig,
    CertificateFilter,
    ValidationConfig,
    SignatureEnvelopeParameters
)

# Configure certificate filtering
cert_filter = CertificateFilter(
    cn="Aniket Chaturvedi",           # Common Name
    o="ManageX",                      # Organization
    email="user@company.com",         # Email from SAN
    ca="Capricorn CA"                 # Issuing CA
)

# Configure validation with trusted root certificates
validation = ValidationConfig(
    check_validity=True,              # Check certificate expiration
    check_revocation_crl=True,        # Check CRL revocation
    check_revocation_ocsp=False,      # Check OCSP revocation
    trusted_roots_folder="root_certificates"  # Folder with trusted root CAs
)

# Create Windows Store configuration
config = WindowsStoreConfig(
    store="MY",
    certificate_filter=cert_filter,
    validation_config=validation
)

# Create XML signer
signer = XMLSigner(config)

# Sign with custom signature parameters
signature_params = SignatureEnvelopeParameters.create_default("ManageX-Signature")
signer.sign_file("document.xml", "signed_document.xml")
```

## 🔧 Command Line Usage

The SDK includes a comprehensive command-line tool compatible with existing workflows:

```bash
# Basic signing with Windows Store (shows certificate selection dialog)
python managex_xml_signing_example.py --use-store --file document.xml

# Sign with specific certificate criteria
python managex_xml_signing_example.py --cn "Aniket" --o "ManageX" --file document.xml

# HSM token signing with PIN protection
python managex_xml_signing_example.py --use-hsm --file document.xml

# PFX file signing
python managex_xml_signing_example.py --use-pfx mycert.pfx --file document.xml

# List available certificates
python managex_xml_signing_example.py --list-certs

# List HSM tokens
python managex_xml_signing_example.py --list-tokens
```

## 📁 Certificate Sources

### 1. Windows Certificate Store
```python
config = WindowsStoreConfig(
    store="MY",  # Personal certificate store
    certificate_filter=CertificateFilter(cn="Your Name"),
    validation_config=ValidationConfig.basic_validation("root_certificates")
)
```

### 2. PFX Files (PKCS#12)
```python
config = PFXConfig(
    pfx_file="certificate.pfx",
    password="your_password",
    certificate_filter=CertificateFilter(cn="Your Name"),
    validation_config=ValidationConfig.basic_validation("root_certificates")
)
```

### 3. HSM Tokens (PKCS#11)
```python
config = HSMConfig(
    dll_path="C:\\Windows\\System32\\eToken.dll",  # Auto-detected if None
    pin="123456",  # Will prompt if not provided
    certificate_filter=CertificateFilter(cn="Your Name"),
    validation_config=ValidationConfig.basic_validation("root_certificates")
)
```

## 🔐 Security Features

### Trusted Root Certificate Validation
Place your trusted root CA certificates in PEM format:
```
root_certificates/
├── CCA_India/
│   └── CCA_India_2022.pem
├── Capricorn/
│   ├── Capricorn_CA_2022.pem
│   └── Capricorn_Sub_CA_Individual_2022.pem
├── eMudhra/
│   └── eMudhra_Root_CA.pem
└── Other_CAs/
    └── custom_ca.pem
```

### Certificate Chain Validation
- **AKI/SKI Matching**: Authority Key Identifier to Subject Key Identifier validation
- **Cryptographic Verification**: Digital signature verification against root CAs
- **Key Usage Validation**: Ensures certificates have proper key usage for signing
- **Revocation Checking**: CRL and OCSP support

### HSM Token Protection
- **PIN Retry Limits**: Prevents token locking with multiple failed attempts
- **Token Status Monitoring**: Checks remaining PIN attempts before proceeding
- **Graceful Abort**: User can cancel operations to prevent token lock

## 📖 API Reference

### Core Classes

#### XMLSigner
Main class for XML signing operations.
```python
signer = XMLSigner(config, signature_params)
signer.sign_file(input_file, output_file)  # Sign file
signed_content = signer.sign_content(xml_bytes)  # Sign content
```

#### Configuration Classes
- `WindowsStoreConfig`: Windows Certificate Store configuration
- `PFXConfig`: PFX file configuration
- `HSMConfig`: HSM token configuration

#### Filter and Validation
- `CertificateFilter`: Certificate selection criteria
- `ValidationConfig`: Certificate validation rules
- `SignatureEnvelopeParameters`: XML signature customization

### Utility Functions

#### Certificate Discovery
```python
from managex_xml_sdk.signers.windows_store_signer import WindowsStoreSigner

signer = WindowsStoreSigner(config)
certificates = signer.get_all_certificates_from_store()
valid_certs = signer.filter_valid_signing_certificates(certificates)
```

#### HSM Token Discovery
```python
from managex_xml_sdk.signers.hsm_signer import HSMSigner

tokens = HSMSigner.get_all_available_tokens()
for token in tokens:
    print(f"Token: {token['label']} - {token['manufacturer']}")
```

## 🧪 Examples

Check the `examples/` directory for comprehensive usage examples:

- `simple_sdk_example.py`: Basic SDK functionality demonstration
- `managex_xml_signing_example.py`: Full command-line tool
- `certificate_discovery_example.py`: Certificate enumeration and filtering
- `hsm_integration_example.py`: HSM token usage examples

## 🛠️ Development

### Prerequisites
- Python 3.8+
- Windows: pywin32, PyKCS11 (for HSM support)
- Linux/macOS: PyKCS11 (for HSM support)

### Setting up Development Environment
```bash
git clone https://github.com/Aniketc068/managex_xml_sdk.git
cd managex_xml_sdk
python -m venv venv
source venv/bin/activate  # Linux/macOS
# or
venv\\Scripts\\activate  # Windows
pip install -r requirements.txt
pip install -e .
```

### Running Tests
```bash
python -m pytest tests/
python simple_sdk_example.py  # Integration test
```

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Guidelines
- Follow PEP 8 style guidelines
- Add unit tests for new features
- Update documentation for API changes
- Ensure cross-platform compatibility

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author & Support

**Aniket Chaturvedi**
- 📧 Email: [chaturvedianiket007@gmail.com](mailto:chaturvedianiket007@gmail.com)
- 🐙 GitHub: [@Aniketc068](https://github.com/Aniketc068)
- 🏢 Organization: ManageX

### Support
- 📧 **Email Support**: [chaturvedianiket007@gmail.com](mailto:chaturvedianiket007@gmail.com)
- 🐛 **Bug Reports**: [GitHub Issues](https://github.com/Aniketc068/managex_xml_sdk/issues)
- 💬 **Feature Requests**: [GitHub Discussions](https://github.com/Aniketc068/managex_xml_sdk/discussions)
- 📖 **Documentation**: [Wiki](https://github.com/Aniketc068/managex_xml_sdk/wiki)

## 🙏 Acknowledgments

- Thanks to all contributors and the open-source community
- Built with ❤️ for the digital certificate and XML signing ecosystem
- Special thanks to collaborators and early adopters

## 📊 Project Status

- ✅ **Stable**: Production ready
- 🔄 **Active Development**: Regular updates and improvements
- 🌍 **Community Driven**: Open to contributions and feedback

---

**Made with ❤️ by [Aniket Chaturvedi](https://github.com/Aniketc068) for ManageX**