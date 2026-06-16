[Email Validator](https://apify.com/giovannibiancia/email-validator?fpr=data)

Advanced email validation actor for Apify platform with comprehensive verification capabilities including SMTP verification, MX record checks, and disposable email detection.

## 🚀 Features

- **📝 Format Validation**: RFC 5322 compliant email format checking
- **🌐 MX Records Check**: Validates domain has valid mail exchange records
- **✉️ SMTP Verification**: Real-time email existence verification via SMTP
- **🔍 Provider Detection**: Identifies free and disposable email providers
- **💡 Smart Suggestions**: Suggests corrections for common domain typos
- **⚡ Batch Processing**: Efficiently processes large email lists
- **🛡️ Rate Limiting**: Built-in delays to prevent abuse
- **📊 Detailed Reporting**: Comprehensive validation results and statistics

## 📋 Input Configuration

### Required Fields

- **`emails`** (array): List of email addresses to validate

### Optional Settings

- **`checkSMTP`** (boolean, default: `true`): Enable SMTP verification
- **`rejectDisposable`** (boolean, default: `false`): Mark disposable emails as invalid
- **`batchSize`** (integer, default: `50`): Number of emails per batch (1-100)
- **`delayBetweenChecks`** (number, default: `0.1`): Delay between validations in seconds
- **`maxRetries`** (integer, default: `2`): Maximum retry attempts for SMTP connections
- **`timeout`** (integer, default: `10`): SMTP connection timeout in seconds

## 📤 Output Format

Each email validation returns a detailed result object:

```
{
  "email": "user@example.com",
  "original_email": "User@Example.com",
  "is_valid": true,
  "valid_format": true,
  "valid_mx": true,
  "valid_smtp": true,
  "is_free": false,
  "is_disposable": false,
  "domain": "example.com",
  "errors": [],
  "warnings": [],
  "suggestions": [],
  "mx_records": ["10:mail.example.com"],
  "smtp_response": "250 OK",
  "validation_time": 1642789123.456
}
```

### Final Summary Object

```
{
  "summary": true,
  "total_emails": 1000,
  "valid_emails": 847,
  "invalid_emails": 153,
  "free_emails": 234,
  "disposable_emails": 12,
  "validation_rate": 84.7,
  "processing_time_seconds": 45.2,
  "emails_per_second": 22.1,
  "common_errors": {
    "Invalid email format": 89,
    "Domain has no MX records": 34,
    "Email address does not exist": 30
  },
  "options_used": {...},
  "checks_performed": [...]
}
```

## 🎯 Use Cases

### Marketing & Lead Generation

- **Email List Cleaning**: Remove invalid emails before campaigns
- **Lead Qualification**: Verify prospect contact information
- **Bounce Rate Reduction**: Improve email deliverability rates

### E-commerce & SaaS

- **User Registration**: Validate emails during signup
- **Account Recovery**: Ensure password reset emails reach users
- **Newsletter Management**: Clean subscriber lists

### Data Quality & Compliance

- **Database Maintenance**: Regular email list hygiene
- **GDPR Compliance**: Verify contact consent accuracy
- **CRM Integration**: Enhance customer data quality

## 🛠️ Technical Details

### Validation Process

1. **Format Check**: RFC 5322 regex validation
2. **Domain Extraction**: Parse email domain
3. **Provider Classification**: Check against free/disposable lists
4. **MX Records**: DNS lookup for mail exchange records
5. **SMTP Verification**: Connect to mail server and verify recipient
6. **Result Compilation**: Generate comprehensive validation report

### Supported Email Providers

#### Free Providers (200+)

- Gmail, Yahoo, Outlook, Hotmail, AOL
- iCloud, Protonmail, Yandex, Mail.ru
- And many more regional providers

#### Disposable Providers (100+)

- 10minutemail, Tempmail, Guerrillamail
- Mailinator, YOPmail, Maildrop
- Comprehensive list of temporary email services

### Performance & Limits

- **Throughput**: Up to 50+ emails/second (depending on SMTP response times)
- **Batch Processing**: Configurable batch sizes (1-100 emails)
- **Rate Limiting**: Customizable delays to prevent blocking
- **Retry Logic**: Automatic retries for failed connections
- **Timeout Handling**: Configurable connection timeouts

## 🚨 Important Notes

### SMTP Verification Considerations

- **Rate Limits**: Some mail servers implement rate limiting
- **False Positives**: Yahoo and some providers may return positive responses for non-existent emails
- **Temporary Failures**: Network issues may cause temporary validation failures
- **Privacy**: SMTP checks involve connecting to external mail servers

### Best Practices

1. **Start Small**: Test with small batches first
2. **Monitor Results**: Check for high error rates or timeouts
3. **Respect Limits**: Use appropriate delays between checks
4. **Handle Failures**: Plan for temporary network issues
5. **Regular Updates**: Disposable provider lists change frequently

## 📊 Example Usage

### Basic Email Validation

```
{
  "emails": [
    "user@gmail.com",
    "test@example.com",
    "invalid@nonexistent.xyz"
  ]
}
```

### Advanced Configuration

```
{
  "emails": ["user1@example.com", "user2@test.com"],
  "checkSMTP": true,
  "rejectDisposable": true,
  "batchSize": 25,
  "delayBetweenChecks": 0.2,
  "maxRetries": 3,
  "timeout": 15
}
```

### Large Scale Processing

```
{
  "emails": [...], // 10,000+ emails
  "checkSMTP": false, // Faster processing
  "batchSize": 100,
  "delayBetweenChecks": 0.05
}
```

## 🔧 Error Handling

The actor handles various error scenarios gracefully:

- **Invalid Input**: Clear error messages for malformed input
- **Network Issues**: Retry logic for temporary failures
- **Rate Limiting**: Automatic backoff and retry
- **Server Errors**: Detailed error reporting and logging
- **Timeout Handling**: Configurable timeouts with fallback

## 📈 Performance Optimization

### Speed vs Accuracy Trade-offs

- **Fast Mode**: Disable SMTP checking for format/MX validation only
- **Balanced Mode**: Default settings with moderate SMTP verification
- **Thorough Mode**: Enable all checks with retries for maximum accuracy

### Batch Size Recommendations

- **Small Lists** (< 100): Batch size 10-25
- **Medium Lists** (100-1000): Batch size 25-50
- **Large Lists** (1000+): Batch size 50-100

## 🆘 Support & Troubleshooting

### Common Issues

1. **High Invalid Rate**: Check for data quality issues in input
2. **Slow Processing**: Reduce batch size or increase delays
3. **SMTP Timeouts**: Increase timeout values or reduce retries
4. **Memory Issues**: Process very large lists in smaller chunks

### Getting Help

- Check actor logs for detailed error information
- Review input format and configuration options
- Contact Apify support for platform-specific issues

## 📄 License & Terms

This actor is provided as-is for email validation purposes. Users are responsible for:

- Complying with applicable data protection laws
- Respecting email provider terms of service
- Using validated data ethically and legally
- Maintaining data security and privacy

---

## 🏷️ Tags

`email` `validation` `smtp` `mx-records` `verification` `email-checker` `disposable-email` `bulk-validation` `data-quality` `lead-generation`

---

*Built with ❤️ for the Apify platform*