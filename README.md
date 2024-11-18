# ETD Explorer with Chatbot


##const [correctedSearchTerm, setCorrectedSearchTerm] = useState('');

const newCorrectedSearchTerm = ['exology', 'ecolgy', 'echology'].includes(value.toLowerCase()) ? 'ecology' : value;

   app.post('/search', async (req, res) => {
  const { apiKey, term, index, page = 1, range } = req.body;
  
  // Validate API key
  if (apiKey !== 'YourExpectedApiKey') {
    return res.status(403).json({ error: 'Invalid API key' });
  }

  const pageSize = range || 10; // Default to 10 if range is not provided
  const from = (page - 1) * pageSize;
  console.log('Search Term:', term);

  try {
    const response = await esClient.search({
      index,
      body: {
        query: {
          multi_match: {
            query: term,
            fields: ["*"], // You can specify fields if needed
            fuzziness: "AUTO"
          }
        },
        from,
        size: pageSize
      }
    });

    console.log('Elasticsearch Response:', response);
    const hits = response.body ? response.body.hits.hits : response.hits.hits;
    const total = response.body ? response.body.hits.total.value : response.hits.total.value;
    // Uncomment and adjust the suggestions part if you're using suggest feature
    // const suggestions = response.body ? response.body.suggest.simple_suggest[0].options : [];
    // console.log('Suggestions:', suggestions);

    res.json({ hits, total, page, pageSize /*, suggestions */ }); 
  } catch (error) {
    console.error('Search error:', error);
    res.status(500).json({ error: error.message });
  }
});

