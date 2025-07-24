# MARKETING-PRO
import React, { useState, useEffect } from 'react';
import { BrowserRouter as Router, Route, Routes, Link, useParams, useNavigate } from 'react-router-dom';

// Main App Component
const App = () => {
    // State for mock data
    const [campaigns, setCampaigns] = useState([
        { id: 1, name: 'Welcome Series', status: 'Draft', date: '2025-07-01', templateId: 1 },
        { id: 2, name: 'Product Launch', status: 'Sent', date: '2025-06-15', templateId: 2 },
    ]);
    const [templates, setTemplates] = useState([
        { id: 1, name: 'Newsletter Template', content: '<h1>Weekly Newsletter</h1><p>Stay updated with our latest news!</p><img src="https://placehold.co/300x150/007bff/ffffff?text=Newsletter+Image" alt="Newsletter Image" class="w-full rounded-md mt-4">' },
        { id: 2, name: 'Promotional Template', content: '<h2>Special Offer!</h2><p>Limited time discount on all products!</p><p>Use code: <strong>SAVEBIG</strong></p><img src="https://placehold.co/300x150/28a745/ffffff?text=Promotion+Image" alt="Promotion Image" class="w-full rounded-md mt-4">' },
    ]);
    const [subscribers, setSubscribers] = useState([
        { id: 1, email: 'john.doe@example.com', status: 'Active' },
        { id: 2, email: 'jane.smith@example.com', status: 'Active' },
    ]);

    // Functions to manage data
    const addTemplate = (newTemplate) => {
        setTemplates([...templates, { id: templates.length > 0 ? Math.max(...templates.map(t => t.id)) + 1 : 1, ...newTemplate }]);
    };
    const updateTemplate = (id, updatedContent) => {
        setTemplates(templates.map(t => t.id === id ? { ...t, content: updatedContent } : t));
    };
    const addSubscriber = (newSubscriber) => {
        setSubscribers([...subscribers, { id: subscribers.length > 0 ? Math.max(...subscribers.map(s => s.id)) + 1 : 1, ...newSubscriber }]);
    };
    const addCampaign = (newCampaign) => {
        setCampaigns([...campaigns, { id: campaigns.length > 0 ? Math.max(...campaigns.map(c => c.id)) + 1 : 1, ...newCampaign }]);
    };

    return (
        <Router>
            <div className="flex min-h-screen bg-gray-100 font-sans">
                {/* Sidebar */}
                <div className="w-64 bg-gray-800 text-white p-6 shadow-lg rounded-r-lg">
                    <h2 className="text-3xl font-bold mb-8 text-green-400 text-center">📧 Marketing App</h2>
                    <ul className="space-y-4">
                        <li>
                            <Link to="/campaigns" className="block p-3 rounded-lg hover:bg-gray-700 transition duration-200">
                                Campaigns
                            </Link>
                        </li>
                        <li>
                            <Link to="/templates" className="block p-3 rounded-lg hover:bg-gray-700 transition duration-200">
                                Templates
                            </Link>
                        </li>
                        <li>
                            <Link to="/subscribers" className="block p-3 rounded-lg hover:bg-gray-700 transition duration-200">
                                Subscribers
                            </Link>
                        </li>
                    </ul>
                </div>

                {/* Main Content */}
                <div className="flex-grow p-8">
                    <Routes>
                        <Route path="/" element={
                            <div className="bg-white p-8 rounded-lg shadow-md text-center">
                                <h2 className="text-4xl font-extrabold text-gray-800 mb-4">Welcome to your Email Marketing App!</h2>
                                <p className="text-lg text-gray-600">Navigate using the sidebar to manage campaigns, templates, and subscribers.</p>
                            </div>
                        } />
                        <Route path="/campaigns" element={<Campaigns campaigns={campaigns} templates={templates} addCampaign={addCampaign} />} />
                        <Route path="/templates" element={<Templates templates={templates} />} />
                        <Route path="/templates/edit/:id" element={<EmailEditor templates={templates} updateTemplate={updateTemplate} />} />
                        <Route path="/templates/new" element={<EmailEditor addTemplate={addTemplate} />} />
                        <Route path="/subscribers" element={<Subscribers subscribers={subscribers} addSubscriber={addSubscriber} />} />
                    </Routes>
                </div>
            </div>
        </Router>
    );
};

// Campaigns Component
const Campaigns = ({ campaigns, templates, addCampaign }) => {
    const [newCampaignName, setNewCampaignName] = useState('');
    const [selectedTemplate, setSelectedTemplate] = useState('');

    const handleAddCampaign = () => {
        if (newCampaignName && selectedTemplate) {
            addCampaign({
                name: newCampaignName,
                status: 'Draft',
                date: new Date().toISOString().slice(0, 10),
                templateId: parseInt(selectedTemplate)
            });
            setNewCampaignName('');
            setSelectedTemplate('');
        } else {
            // Using a simple alert for now, in a real app use a modal/toast
            console.error('Please provide a campaign name and select a template.');
        }
    };

    return (
        <div className="bg-white p-8 rounded-lg shadow-md">
            <h3 className="text-2xl font-semibold mb-6 text-gray-800">Campaigns</h3>
            <div className="flex flex-col md:flex-row items-center space-y-4 md:space-y-0 md:space-x-4 mb-8">
                <input
                    type="text"
                    placeholder="Campaign Name"
                    value={newCampaignName}
                    onChange={(e) => setNewCampaignName(e.target.value)}
                    className="flex-grow p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
                />
                <select
                    value={selectedTemplate}
                    onChange={(e) => setSelectedTemplate(e.target.value)}
                    className="p-3 border border-gray-300 rounded-lg bg-white focus:outline-none focus:ring-2 focus:ring-blue-500"
                >
                    <option value="">Select Template</option>
                    {templates.map(template => (
                        <option key={template.id} value={template.id}>{template.name}</option>
                    ))}
                </select>
                <button
                    onClick={handleAddCampaign}
                    className="px-6 py-3 bg-blue-600 text-white font-bold rounded-lg shadow-md hover:bg-blue-700 transition duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                >
                    Add Campaign
                </button>
            </div>

            <div className="overflow-x-auto">
                <table className="min-w-full bg-white rounded-lg shadow-sm">
                    <thead>
                        <tr className="bg-gray-200 text-gray-700 uppercase text-sm leading-normal">
                            <th className="py-3 px-6 text-left rounded-tl-lg">Name</th>
                            <th className="py-3 px-6 text-left">Status</th>
                            <th className="py-3 px-6 text-left">Date</th>
                            <th className="py-3 px-6 text-left rounded-tr-lg">Template</th>
                        </tr>
                    </thead>
                    <tbody className="text-gray-600 text-sm font-light">
                        {campaigns.map(campaign => (
                            <tr key={campaign.id} className="border-b border-gray-200 hover:bg-gray-100">
                                <td className="py-3 px-6 text-left whitespace-nowrap">{campaign.name}</td>
                                <td className="py-3 px-6 text-left">{campaign.status}</td>
                                <td className="py-3 px-6 text-left">{campaign.date}</td>
                                <td className="py-3 px-6 text-left">
                                    {templates.find(t => t.id === campaign.templateId)?.name || 'N/A'}
                                </td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
        </div>
    );
};

// Templates Component
const Templates = ({ templates }) => {
    return (
        <div className="bg-white p-8 rounded-lg shadow-md">
            <h3 className="text-2xl font-semibold mb-6 text-gray-800">Email Templates</h3>
            <Link to="/templates/new">
                <button className="px-6 py-3 bg-green-600 text-white font-bold rounded-lg shadow-md hover:bg-green-700 transition duration-200 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2 mb-6">
                    Create New Template
                </button>
            </Link>
            <div className="overflow-x-auto">
                <table className="min-w-full bg-white rounded-lg shadow-sm">
                    <thead>
                        <tr className="bg-gray-200 text-gray-700 uppercase text-sm leading-normal">
                            <th className="py-3 px-6 text-left rounded-tl-lg">Name</th>
                            <th className="py-3 px-6 text-left rounded-tr-lg">Actions</th>
                        </tr>
                    </thead>
                    <tbody className="text-gray-600 text-sm font-light">
                        {templates.map(template => (
                            <tr key={template.id} className="border-b border-gray-200 hover:bg-gray-100">
                                <td className="py-3 px-6 text-left whitespace-nowrap">{template.name}</td>
                                <td className="py-3 px-6 text-left">
                                    <Link to={/templates/edit/${template.id}} className="text-blue-600 hover:underline">
                                        Edit
                                    </Link>
                                </td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
        </div>
    );
};

// EmailEditor Component
const EmailEditor = ({ templates, addTemplate, updateTemplate }) => {
    const { id } = useParams(); // Get ID from URL for editing
    const navigate = useNavigate();
    const [templateName, setTemplateName] = useState('');
    const [templateContent, setTemplateContent] = useState('');

    useEffect(() => {
        if (id) {
            // Editing existing template
            const existingTemplate = templates.find(t => t.id === parseInt(id));
            if (existingTemplate) {
                setTemplateName(existingTemplate.name);
                setTemplateContent(existingTemplate.content);
            }
        } else {
            // Reset for new template creation
            setTemplateName('');
            setTemplateContent('');
        }
    }, [id, templates]);

    const handleSave = () => {
        if (!templateName.trim() || !templateContent.trim()) {
            console.error('Template name and content cannot be empty.');
            return;
        }

        if (id) {
            updateTemplate(parseInt(id), templateContent);
        } else {
            addTemplate({ name: templateName, content: templateContent });
        }
        navigate('/templates'); // Go back to templates list
    };

    return (
        <div className="bg-white p-8 rounded-lg shadow-md">
            <h3 className="text-2xl font-semibold mb-6 text-gray-800">{id ? 'Edit Template' : 'Create New Template'}</h3>
            <div className="mb-4">
                <label htmlFor="templateName" className="block text-gray-700 text-sm font-bold mb-2">Template Name:</label>
                <input
                    id="templateName"
                    type="text"
                    placeholder="Enter template name"
                    value={templateName}
                    onChange={(e) => setTemplateName(e.target.value)}
                    readOnly={!!id} // Name is read-only for existing templates
                    className="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
                />
            </div>
            <div className="mb-6">
                <label htmlFor="templateContent" className="block text-gray-700 text-sm font-bold mb-2">Email Content (HTML):</label>
                <textarea
                    id="templateContent"
                    placeholder="Enter email content (HTML)"
                    value={templateContent}
                    onChange={(e) => setTemplateContent(e.target.value)}
                    rows="15"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 font-mono"
                ></textarea>
            </div>
            <button
                onClick={handleSave}
                className="px-6 py-3 bg-blue-600 text-white font-bold rounded-lg shadow-md hover:bg-blue-700 transition duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
            >
                Save Template
            </button>

            {templateContent && (
                <div className="mt-8 p-6 border border-gray-200 rounded-lg bg-gray-50 shadow-inner">
                    <h4 className="text-xl font-semibold mb-4 text-gray-800">Preview:</h4>
                    <div
                        className="prose max-w-none bg-white p-4 rounded-md shadow-sm border border-gray-100"
                        dangerouslySetInnerHTML={{ __html: templateContent }}
                    ></div>
                </div>
            )}
        </div>
    );
};

// Subscribers Component
const Subscribers = ({ subscribers, addSubscriber }) => {
    const [newSubscriberEmail, setNewSubscriberEmail] = useState('');
    const [message, setMessage] = useState('');

    const handleAddSubscriber = () => {
        if (!newSubscriberEmail.trim()) {
            setMessage('Email cannot be empty.');
            return;
        }
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(newSubscriberEmail)) {
            setMessage('Please enter a valid email address.');
            return;
        }
        if (subscribers.some(s => s.email === newSubscriberEmail)) {
            setMessage('Subscriber already exists!');
            return;
        }

        addSubscriber({ email: newSubscriberEmail, status: 'Active' });
        setNewSubscriberEmail('');
        setMessage('Subscriber added successfully!');
        setTimeout(() => setMessage(''), 3000); // Clear message after 3 seconds
    };

    return (
        <div className="bg-white p-8 rounded-lg shadow-md">
            <h3 className="text-2xl font-semibold mb-6 text-gray-800">Subscribers</h3>
            <div className="flex flex-col md:flex-row items-center space-y-4 md:space-y-0 md:space-x-4 mb-6">
                <input
                    type="email"
                    placeholder="Add new subscriber email"
                    value={newSubscriberEmail}
                    onChange={(e) => {
                        setNewSubscriberEmail(e.target.value);
                        setMessage(''); // Clear message on input change
                    }}
                    className="flex-grow p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
                />
                <button
                    onClick={handleAddSubscriber}
                    className="px-6 py-3 bg-blue-600 text-white font-bold rounded-lg shadow-md hover:bg-blue-700 transition duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                >
                    Add Subscriber
                </button>
            </div>
            {message && (
                <div className="mb-4 p-3 text-sm text-center text-red-700 bg-red-100 border border-red-200 rounded-lg">
                    {message}
                </div>
            )}

            <div className="overflow-x-auto">
                <table className="min-w-full bg-white rounded-lg shadow-sm">
                    <thead>
                        <tr className="bg-gray-200 text-gray-700 uppercase text-sm leading-normal">
                            <th className="py-3 px-6 text-left rounded-tl-lg">Email</th>
                            <th className="py-3 px-6 text-left rounded-tr-lg">Status</th>
                        </tr>
                    </thead>
                    <tbody className="text-gray-600 text-sm font-light">
                        {subscribers.map(subscriber => (
                            <tr key={subscriber.id} className="border-b border-gray-200 hover:bg-gray-100">
                                <td className="py-3 px-6 text-left whitespace-nowrap">{subscriber.email}</td>
                                <td className="py-3 px-6 text-left">{subscriber.status}</td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
        </div>
    );
};

export default App;
